# Excalidraw fix httpStorage
This **temporary repository** is meant to **simplify collaboration** on creating an up to date version of `httpStorage.ts` and `StorageBackend.ts` which then can be used as "drop-in replacement" for Firebase storage in Excalidraw.

## Goal
I want to provide an **up-to-date** and self-hostable version of **Excalidraw** (not an older version) with the ability to use another storage backend than Firebase.

## Why?
As long as there's no upstream support for alternative backends you can't use Excalidraw with a custom storage solution. As some might not want to use Google services I try to provide an up-to-date alternative.

## What do we have?
We can already play around with the great work from [@kiliandeca](https://gitlab.com/kiliandeca), [@b310-digital](https://github.com/b310-digital) and [@squatica](https://github.com/squatica). It is already possible to combine fork [b310-digital/excalidraw](https://github.com/b310-digital/excalidraw) and [this PR from squatica](https://github.com/b310-digital/excalidraw/pull/1) to build **a working version of Excalidraw with working collaboration features and a HTTP-based storage backend**. But this version is **outdated**. As updating to the recent version of Excalidraw fails due to some bigger modifications **I need some help to bring it up again**.

## How?
I'm already working on a [forked repository](https://github.com/SWEETGOOD-DE/excalidraw-selfhosted) which is implementing all the fixes / repositories mentioned above but the build process does not succeed because some relevant parts of the code have changed in the meantime. And my programming skills regarding TypeScript are not that good to fix that on my own. So I will **share all my findings here** and maybe we can bring up a new working version of this together 🙏

So feel free to collaborate on this 🥳

---

## Repository structure
I first committed the **unmodified files** from the **old repositories** the modifications were based on. Then I committed the [**modified files** from the **working (but outdated) forks**](https://github.com/codiflow/excalidraw-fix-httpstorage/pull/3/commits/c4696333639445a6c1205f2f91b7b5bdf4d63f6b). At least I committed [the recent versions of the upstream repository without modifications](https://github.com/codiflow/excalidraw-fix-httpstorage/pull/2/commits/6cfc8bcdc485622918ad211a93238ade90ed930c). This way we can easily compare the differences and create a working version of httpBackend.

## What I found out so far
There were changes within the function `reconcileElements`. It has also been moved to `../../packages/excalidraw` / `../../packages/excalidraw/data/reconcile` now.  
A new **private** function `_reconcileElements` was introduced with [this commit](https://github.com/excalidraw/excalidraw/commit/32df5502aeaf7899da46ab135016101514a06ecb).  
Some things have changed in `firestore.ts` making the current implementation of `httpStorage.ts` incompatible.

## Current build failures
```
 > [excalidraw production_buildstage 6/6] RUN yarn build:app:docker:                                                                                                                                                                                                                      
0.341 yarn run v1.22.19                                                                                                                                                                                                                                                                   
0.367 $ yarn --cwd ./excalidraw-app build:app:docker                                                                                                                                                                                                                                      
0.556 $ cross-env VITE_APP_DISABLE_SENTRY=true VITE_APP_DISABLE_TRACKING=true VITE_APP_ENABLE_ESLINT=false vite build                                                                                                                                                                     
1.504 vite v5.0.12 building for production...                                                                                                                                                                                                                                             
1.557 transforming...
17.84 collab/Collab.tsx(487,30): error TS2345: Argument of type '{ elements: readonly ExcalidrawElement[]; scrollToContent: boolean; } | null' is not assignable to parameter of type 'MaybePromise<(ImportedDataState & { elements: readonly OrderedExcalidrawElement[]; }) | null>'.
17.84 collab/Collab.tsx(648,28): error TS2345: Argument of type '{ elements: readonly ExcalidrawElement[]; scrollToContent: boolean; } | null' is not assignable to parameter of type 'MaybePromise<(ImportedDataState & { elements: readonly OrderedExcalidrawElement[]; }) | null>'.
17.84   Type '{ elements: readonly ExcalidrawElement[]; scrollToContent: boolean; }' is not assignable to type 'MaybePromise<(ImportedDataState & { elements: readonly OrderedExcalidrawElement[]; }) | null>'.
17.84     Type '{ elements: readonly ExcalidrawElement[]; scrollToContent: boolean; }' is not assignable to type 'ImportedDataState & { elements: readonly OrderedExcalidrawElement[]; }'.
17.84       Type '{ elements: readonly ExcalidrawElement[]; scrollToContent: boolean; }' is not assignable to type '{ elements: readonly OrderedExcalidrawElement[]; }'.
17.84         Types of property 'elements' are incompatible.
17.84           Type 'readonly ExcalidrawElement[]' is not assignable to type 'readonly OrderedExcalidrawElement[]'.
17.84             Type 'ExcalidrawElement' is not assignable to type 'OrderedExcalidrawElement'.
17.84               Type 'ExcalidrawSelectionElement' is not assignable to type 'OrderedExcalidrawElement'.
17.84                 Type 'ExcalidrawSelectionElement' is not assignable to type 'Readonly<{ id: string; x: number; y: number; strokeColor: string; backgroundColor: string; fillStyle: FillStyle; strokeWidth: number; strokeStyle: StrokeStyle; roundness: { ...; } | null; ... 16 more ...; customData?: Record<...> | undefined; }> & { ...; } & { ...; }'.
17.84                   Type 'ExcalidrawSelectionElement' is not assignable to type '{ index: FractionalIndex; }'.
17.84                     Types of property 'index' are incompatible.
17.84                       Type 'FractionalIndex | null' is not assignable to type 'FractionalIndex'.
17.84                         Type 'null' is not assignable to type 'FractionalIndex'.
17.84                           Type 'null' is not assignable to type 'string'.
17.84 data/config.ts(27,5): error TS2322: Type '(portal: Portal, elements: readonly SyncableExcalidrawElement[], appState: AppState) => Promise<false | { reconciledElements: null; } | { reconciledElements: readonly SyncableExcalidrawElement[]; }>' is not assignable to type '(portal: Portal, elements: readonly SyncableExcalidrawElement[], appState: AppState) => Promise<readonly ExcalidrawElement[] | null>'.
17.84   Type 'Promise<false | { reconciledElements: null; } | { reconciledElements: readonly SyncableExcalidrawElement[]; }>' is not assignable to type 'Promise<readonly ExcalidrawElement[] | null>'.
17.84     Type 'false | { reconciledElements: null; } | { reconciledElements: readonly SyncableExcalidrawElement[]; }' is not assignable to type 'readonly ExcalidrawElement[] | null'.
17.84       Type 'boolean' is not assignable to type 'readonly ExcalidrawElement[]'.
23.34 ✓ 2506 modules transformed.
24.68 rendering chunks...
24.86 error Command failed with exit code 2.
24.86 info Visit https://yarnpkg.com/en/docs/cli/run for documentation about this command.
24.88 error Command failed with exit code 2.
24.89 info Visit https://yarnpkg.com/en/docs/cli/run for documentation about this command.
```
