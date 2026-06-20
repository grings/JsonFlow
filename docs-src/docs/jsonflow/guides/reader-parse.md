---
title: Reader / parser
sidebar_position: 4
---

# Reader / parser

`TJSONReader` (unit `JsonFlow.Reader`) implements `IJSONReader`. It converts a JSON string or stream into an `IJSONElement` tree.

## Parse a JSON string

```delphi
uses
  JsonFlow.Reader,
  JsonFlow.Interfaces;

var
  LReader: TJSONReader;
  LRoot: IJSONElement;
  LObj: IJSONObject;
begin
  LReader := TJSONReader.Create;
  try
    LRoot := LReader.Read('{"user":"Alice","active":true}');

    // Cast to IJSONObject
    if Supports(LRoot, IJSONObject, LObj) then
      WriteLn(LObj.GetValue('user').AsJSON); // "Alice"
  finally
    LReader.Free;
  end;
end;
```

## Parse from a stream

```delphi
LRoot := LReader.ReadFromStream(LFileStream);
```

The stream reader uses an internal buffer (default 64 KB) for memory-efficient large-file parsing.

## Constructor overloads

| Constructor | Description |
|---|---|
| `Create` | Default settings (en-US, 64 KB buffer) |
| `Create(ABufferSize)` | Custom buffer size (minimum 4 KB) |
| `Create(AFormatSettings)` | Custom format settings |
| `Create(AFormatSettings, ABufferSize)` | Both custom |

## Progress reporting

```delphi
LReader.OnProgress(
  procedure(ASender: TObject; APercent: Single)
  begin
    WriteLn(Format('Parsed %.0f%%', [APercent]));
  end
);
```

Progress is reported during stream parsing only.

## Logging

```delphi
LReader.OnLog(
  procedure(const AMsg: string) begin WriteLn(AMsg); end
);
```

## Automatic ISO 8601 detection

String values matching the ISO 8601 date/datetime pattern (`YYYY-MM-DD` or `YYYY-MM-DDThh:mm:ss`) are automatically parsed as `TJSONValueDateTime` rather than `TJSONValueString`. Disable this by using a custom middleware or by checking `TypeName` on the returned element.

## Error handling

All parse errors raise `EJsonFlowParseError` (descendant of `Exception`). Common scenarios:

| Exception message | Cause |
|---|---|
| `Empty JSON string` | Input is `''` |
| `Unexpected end of JSON` | Truncated input |
| `Invalid escape sequence` | Bad `\x` in a string |
| `Extra characters after JSON` | Trailing garbage after valid JSON |

## IJSONReader interface

```delphi
IJSONReader = interface
  function Read(const AJson: String): IJSONElement;
  function ReadFromStream(AStream: TStream): IJSONElement;
  procedure OnLog(const ALogProc: TProc<String>);
  procedure OnProgress(const AProgress: TProc<TObject, Single>);
end;
```

## Navigating the parsed tree

After parsing, use `TJSONNavigator` (unit `JsonFlow.Navigator`) to navigate by path:

```delphi
uses
  JsonFlow.Navigator;

var
  LNav: TJSONNavigator;
begin
  LNav := TJSONNavigator.Create(LRoot);
  try
    WriteLn(LNav.GetString('user.address.city'));
    WriteLn(LNav.GetInteger('items[0].qty'));
  finally
    LNav.Free;
  end;
end;
```

See [Composer / dynamic editing](./composer-dynamic-editing) for mutating the tree after parsing.
