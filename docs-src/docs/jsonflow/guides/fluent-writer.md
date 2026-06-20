---
title: Fluent JSON writer
sidebar_position: 3
---

# Fluent JSON writer

`TJSONWriter` (unit `JsonFlow.Writer`) implements `IJSONWriter`. It converts an `IJSONElement` tree to a JSON string or stream.

## Basic usage via the TJsonFlow facade

```delphi
uses
  JsonFlow,
  JsonFlow.Interfaces,
  JsonFlow.Objects,
  JsonFlow.Arrays,
  JsonFlow.Value;

var
  LObj: IJSONObject;
  LJson: string;
begin
  LObj := TJSONObject.Create;
  LObj.Add('name', TJSONValueString.Create('Alice'));
  LObj.Add('age', TJSONValueInteger.Create(28));

  // Compact output
  LJson := TJsonFlow.Write.Write(LObj);
  // {"name":"Alice","age":28}

  // Indented (pretty-print)
  LJson := TJsonFlow.Write.Write(LObj, {AIdent=}True);
end;
```

## Direct TJSONWriter usage

```delphi
uses
  JsonFlow.Writer,
  JsonFlow.Interfaces;

var
  LWriter: TJSONWriter;
begin
  LWriter := TJSONWriter.Create;
  try
    LJson := LWriter.Write(LElement, {AIdent=}False);
  finally
    LWriter.Free;
  end;
end;
```

### Constructor overloads

| Constructor | Description |
|---|---|
| `Create` | Default format: en-US, ISO dates, `.` decimal separator |
| `Create(AFormatSettings)` | Custom format settings |

## Writing to a stream

```delphi
LWriter.WriteToStream(LElement, LStream, {AIdent=}False);
```

Encodes the output as UTF-8.

## Logging

```delphi
LWriter.OnLog(
  procedure(const AMsg: string) begin WriteLn(AMsg); end
);
```

## IJSONWriter interface

```delphi
IJSONWriter = interface
  function Write(const AElement: IJSONElement; const AIdent: Boolean = False): String;
  procedure WriteToStream(const AElement: IJSONElement; AStream: TStream; const AIdent: Boolean = False);
  procedure OnLog(const ALogProc: TProc<String>);
end;
```

## Building the element tree

Use the composition classes (`JsonFlow.Objects`, `JsonFlow.Arrays`, `JsonFlow.Value`) to build the tree before writing:

| Class | Interface | Use for |
|---|---|---|
| `TJSONObject` | `IJSONObject` | JSON object `{}` |
| `TJSONArray` | `IJSONArray` | JSON array `[]` |
| `TJSONValueString` | `IJSONValue` | string value |
| `TJSONValueInteger` | `IJSONValue` | integer value |
| `TJSONValueFloat` | `IJSONValue` | float value |
| `TJSONValueBoolean` | `IJSONValue` | boolean value |
| `TJSONValueNull` | `IJSONValue` | null value |
| `TJSONValueDateTime` | `IJSONValue` | ISO 8601 datetime |

:::tip Alternative: use TJSONComposer
For building JSON programmatically without manually constructing the element tree, prefer [`TJSONComposer`](./composer-dynamic-editing) — it provides a fully fluent `Add(...).BeginObject(...).EndObject` API.
:::
