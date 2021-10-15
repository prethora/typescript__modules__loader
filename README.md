# Loader

Manages loading of the main application, subsequent application chunks and images in the background. Emits events for a progress indicator.

## Inputs

Inputs are sourced from the following global object map:

`window.__loader_global__`

The loader does not create the object, it should be created by the input provider (if it does not already exist).

#### .mainApplication

The url to the main application script.

Once loaded, the loader will artificially fire a window "load" event (at which point one will have already been fired naturally by the browser).

#### .manifest

A map of resources to load/wait for, for each application chunk. The main application chunk should use key `main`. The other keys should correspond to the application chunk `id`s.

E.g.

```javascript
{
    main: {
        images: ["...image url 1","...image url 2"],
        fonts: [{ name: "font-family-name1", weight: 400 },{ name: "font-family-name2", weight: 300 }]
    },
    about: {...},
    store: {...},
    ...
}
```
#### .config

A map of optional configurable values. The `.config` map may be omitted completely.

E.g.

```javascript
{
    progress_exit_duration: 0.3 //in seconds, 0.3 is the default if not provided.
}
```

## Activation

The loader activates automatically upon page load, and doesn't require any manual function call. It will immediately begin loading resources, emitting progress events and starting the application when ready.

## Outputs

#### progress

Will emit a `loader-progress` event on the window object with the following possible event data:

* `{type: "progress",ratio: 0.73}` (display progress for 73% complete)
* `{type: "complete"}` (display progress for 100% complete, and prepare to receive css class for visual exit - i.e. make sure sudden scrollbar display doesn't cause jumpiness)

#### body classes

Will add the following classes to the `body` of the page at corresponding moments:

* `loader-progress-exit` - added after `loader-progress` event with `type: "complete"` is emitted. The progress indicator should respond to this class by removing itself from the display, preferably with an animation of duration `.config.progress_exit_duration` (defaults to 0.3s).
* `loader-start-application` - added `.config.progress_exit_duration` (defaults to 0.3s) seconds after class `loader-progress-exit` is added. Application should respond to this class by displaying itself.