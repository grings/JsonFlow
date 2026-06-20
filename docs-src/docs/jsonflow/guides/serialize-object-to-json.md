---
title: Serialize object to JSON
sidebar_position: 1
---

# Serialize object to JSON

JsonFlow uses RTTI to inspect published and public properties and convert them to a JSON object. The primary entry points are `TJsonFlow` (static facade) and `TJSONSerializer` (configurable instance).

## Using the TJsonFlow facade

```delphi
uses
  JsonFlow;

// Single object
LJson := TJsonFlow.ObjectToJsonString(LUser);

// Object list (non-generic)
LJson := TJsonFlow.ObjectListToJsonString(LObjectList);

// Object list (generic)
LJson := TJsonFlow.ObjectListToJsonString<TUser>(LUserList);
```

The optional `AStoreClassName` flag adds a `"$ClassName"` field to the output — useful for polymorphic round-trips.

## Using TJSONSerializer directly

`TJSONSerializer` (unit `JsonFlow.Serializer`) exposes a configurable API:

```delphi
uses
  JsonFlow.Serializer,
  JsonFlow.Interfaces;

var
  LSerializer: TJSONSerializer;
  LElement: IJSONElement;
begin
  LSerializer := TJSONSerializer.Create;
  try
    // Object → IJSONElement (interface, not string)
    LElement := LSerializer.FromObject(LUser);

    // Back to object
    LSerializer.ToObject(LElement, LUserCopy);
  finally
    LSerializer.Free;
  end;
end;
```

### Constructor overloads

| Constructor | Description |
|---|---|
| `Create` | Default format settings (en-US decimal separator) |
| `Create(AFormatSettings, AUseISO8601)` | Custom format settings; ISO 8601 datetime flag |
| `Create(AOptions)` | Full `TJSONSerializerOptions` record |

### TJSONSerializerOptions

```delphi
type
  TJSONSerializerOptions = record
    UsePool: Boolean;                          // Use internal object pool
    DetectCircularReferences: Boolean;         // Detect and handle circular refs
    CircularReferenceStrategy: TCircularReferenceStrategy;
    ProcessAttributes: Boolean;                // Honor [JSONIgnore], [JSONName], etc.
    IgnoreNullValues: Boolean;                 // Skip null properties in output
    DateTimeFormat: string;                    // Custom DateTime format string
    FloatFormat: string;                       // Custom float format string
    MaxDepth: Integer;                         // Max object graph depth
  end;
```

Call `TJSONSerializerOptions.Default` for sensible defaults.

## Middleware on serialization

Attach middleware to intercept and transform property values during serialization:

```delphi
LSerializer.Middlewares.Add(LMyMiddleware);
```

Middleware must implement `IGetValueMiddleware` or `ISetValueMiddleware` (see [`JsonFlow.Interfaces`](../reference/interfaces)).

## Logging

```delphi
LSerializer.OnLog(
  procedure(const AMsg: string) begin WriteLn(AMsg); end
);
```

:::tip Object lists
Both `ObjectListToJsonString` overloads return a JSON array string. The generic variant (`ObjectListToJsonString<T>`) works with `TObjectList<T>`; the non-generic variant works with `TObjectList<TObject>`.
:::
