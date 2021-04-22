# State Management

## Overview
State management is the means by which settings and configurations of an application are stored and applied.  It is not associated with the collection or storage of human consumable content or contributions.

## Ingredients
There are only 4 parts to state management:

1. State Artifact
1. Collection
1. Storage
1. Recovery

### State Artifact
A state artifact is one or more data structures that retain an application's configuration.  This is certainly the single most important consideration of state management.  My preference is to use a single object that stores a collection of similar items where an item is a uniquely separated area of content or functionality as determined by your application.  Contrary to many frameworks I strongly suggest separating the state artifact from the components represented by that state information.

### Collection
Collection is the means by which the state artifact is updated.  My preference is to write to the state artifact directly for a given piece of functionality, but many application frameworks will provide an API to solve this issue because they do not provide a single state artifact.  I strongly suggest state updates via abstraction only as necessary to eliminate repetition in your code.

### Storage
Storage is the means of writing the state artifact to a file.  In the case of web technologies you have several different options: [cookies](https://developer.mozilla.org/en-US/docs/Web/HTTP/Cookies), [local storage](https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage), [IndexedDB](https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API), the file system, or a server side application.  I strongly recommend not using cookies, because they are limited in size to 4kb per domain and are the slowest option available.  My personal preference is to write state to the local file system if you are running local services, because this allows computer wide availability of the state data without a performance penalty.  If a local service is not running on the given computer I recommend storing state data in either local storage or IndexedDB because they allow for a large volume of data storage without penalty of waiting on the network or a distant server.

### Recovery
Recovery is the means of reading state artifacts from storage and applying this data to the application.  A well written application only needs to apply state recovery on start up or page load.  Many web frameworks will either ignore state recovery on page load or apply it every time a component loads or becomes available, which are both poor practices.

## Example using Shared File Systems
[Shared File Systems](https://github.com/prettydiff/share-file-systems) is a peer-to-peer file system application I wrote in TypeScript with a full Windows-like GUI.  In this application the various *windows* are internally referred to as modals.  Each modal has a uniform data structure even though their content and functionality may differ wildly.  I use a TypeScript interface named *ui_data* as my single storage artifact and the modals are defined as interface *modal*:


```typescript
interface ui_data {
    audio: boolean;
    brotli: brotli;          // 0-13
    color: colorScheme;      // a choice of string names
    colors: colors;          // a data structure of two colors applied to allowed agents
    hashDevice: string;
    hashType: hash;          // a choice of string values for user's choice of hash functions
    hashUser: string;
    modals: {
        [key:string]: modal;
    };
    modalTypes: modalType[]; // a set of strings indicating the types of modals current opened to the user
    nameDevice: string;
    nameUser: string;
    storage: string;
    zIndex: number;
}
interface modal {
    agent: string;
    agentType: agentType;
    callback?: () => void;
    content: Element;
    focus?: Element;
    height?: number;
    history?: string[];
    id?: string;
    inputs?: ui_input[];
    left?: number;
    move?: boolean;
    read_only: boolean;
    resize?: boolean;
    scroll?: boolean;
    search?: [string, string];
    selection?: {
        [key:string]: string;
    };
    share?: string;
    single?: boolean;
    status?: modalStatus;
    status_bar?: boolean;
    status_text?: string;
    text_event?: EventHandlerNonNull;
    text_placeholder?: string;
    text_value?: string;
    timer?: number;
    title: string;
    top?: number;
    type: modalType;
    width?: number;
    zIndex?: number;
}
```

That is the entirety of the state artifact for that application.

The state data is updated on each user interaction by directly writing to that object.  There is one exception, file system modals.  In that case the state artifact is updated through a function, because this change requires multiple changes to a given modal.  The abstraction allows for specifying these changes once in the code.

The application runs a local service and stores the state artifact on the file system in a file named *configuration.json* via an XHR call to the local service.  This occurs frequently.  The slowest part of this process is actually writing files to disk.  The application addresses this by first writing to a randomly named file and then renaming that randomly named file to the correct *configuration.json* thereby overwriting the older data.  It is faster to rename a file than to write a new file, so this effort is used to prevent conflicts and race conditions that may arise from writing to disk too frequently.

The recovery process involves multiple steps.  When the page is requested from the local service the *configuration.json* file is read from disk as well as the page's HTML file and other stored data.  The contents of the configuration.json file, and some other data, are embedded into an HTML comment and injected into the bottom of the page HTML.  The local service responds with the HTML.  A JavaScript file executes automatically as the page is parsed into the browser and this JavaScript walks the DOM to find that comment and extracts the embedded data, which is then assigned to the state artifact data object over the default data structure.  Using the state data the JavaScript recreates each user interface artifact order to the parameters provided in the state artifact.