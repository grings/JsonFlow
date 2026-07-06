# JsonFlow vs X-SuperObject Benchmark

Benchmark comparing **JsonFlow** against [X-SuperObject](https://github.com/onryldz/x-superobject), using the same Neon-derived methodology as the sibling `JsonFlowBenchmark` example (JsonFlow vs native `TJSON`).

## Requirements

The project expects X-SuperObject sources at `../../../temp_x_superobject` (JsonFlow repository root). Clone it there:

```sh
git clone --depth 1 https://github.com/onryldz/x-superobject.git temp_x_superobject
```

Or adjust the project's unit search path to your own X-SuperObject location.

## Data files

The JSON datasets (~31 MB) are **not duplicated** here — the app automatically falls back to `../JsonFlowBenchmark/Data/Benchmarks` when no local `Data\Benchmarks` folder exists next to the executable.

## Methodology notes

- Identical entities and datasets for both libraries; JSON **text parsing is excluded** from the timings on both sides (charts measure pure object marshalling).
- Deserialization pairing: JsonFlow `TJSONSerializer.ToObject(elem, obj)` vs X-SuperObject `TObject.AssignFromJSON(ISuperObject)` — both fill an existing envelope from a pre-parsed document.
- X-SuperObject does not support string enums (enums are strictly ordinal — see `XSuperObject.pas`, `tkEnumeration`). Its input therefore receives enum values converted to ordinals during the non-timed preparation phase; its output contains enums as integers and dates as full ISO datetimes — the library's native conventions.
- Publish numbers from **Release** builds only; prefer the median of 3+ runs.
