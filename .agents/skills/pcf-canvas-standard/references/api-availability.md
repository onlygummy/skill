# PCF Canvas Standard — API Availability

## Canvas vs Model-driven

| API | Canvas | Model-driven | Notes |
|-----|--------|--------------|-------|
| `context.device.*` | ✅ | ✅ | captureImage, captureBarcode, getCurrentPosition, pickFile, etc. |
| `context.webAPI` | ❌ | ✅ | "Method not implemented" in Canvas. Use Canvas App for data. |
| `context.mode.trackContainerResize` | ✅ | ✅ | |
| `context.formatting` | ✅ | ✅ | |
| `context.parameters` | ✅ | ✅ | |
| `context.navigation` | ✅ | ✅ | |
| `context.resources` | ✅ | ✅ | |
| `context.utils` (getEntityMetadata, lookupObjects) | ✅ | ✅ | |
| `Xrm` namespace | ❌ | ✅ | Not available in Canvas. |

## Device APIs (available in Canvas)

- `context.device.captureAudio()`
- `context.device.captureImage()`
- `context.device.captureVideo()`
- `context.device.getBarcodeValue()`
- `context.device.getCurrentPosition()`
- `context.device.pickFile()`

Must declare in manifest `<feature-usage>`.

## context.webAPI (model-driven only)

Methods: createRecord, retrieveRecord, retrieveMultipleRecords, updateRecord, deleteRecord.

In Canvas Apps → runtime error "Method not implemented". Never use in Canvas-targeted controls.

## Xrm namespace

Not available in Canvas Apps. Use `context` APIs instead for portability.

## Power Pages

Device APIs are NOT supported in Power Pages. `uses-feature` must not be set to true.
