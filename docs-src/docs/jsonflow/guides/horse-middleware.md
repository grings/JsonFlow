---
title: Horse middleware
sidebar_position: 11
---

# Horse middleware

`Horse.JsonFlow` (unit `Horse.JsonFlow`) integrates JsonFlow with the [Horse](https://github.com/HashLoad/horse) web framework. It provides automatic JSON body parsing for POST / PUT / PATCH requests and adds a typed `Body<T>` helper on `THorseRequest`.

## Registration

```delphi
uses
  Horse,
  Horse.JsonFlow;

begin
  THorse.Use(HorseJsonFlow);   // default charset: UTF-8, en-US format
  // or
  THorse.Use(HorseJsonFlow('UTF-8'));

  THorse.Post('/users',
    procedure(Req: THorseRequest; Res: THorseResponse; Next: TNextProc)
    var
      LUser: TUser;
    begin
      LUser := Req.Body<TUser>;
      try
        // LUser is populated from the JSON request body
        Res.Send('Created: ' + LUser.Name);
      finally
        LUser.Free;
      end;
    end
  );

  THorse.Listen(9000);
end.
```

## What the middleware does

1. Checks if the request method is POST, PUT, or PATCH.
2. Reads the raw body string from `THorseRequest`.
3. Uses `TJsonFlow.JsonToObject<T>` to deserialize the body into the requested type `T`.
4. The `THorseRequestHelper.Body<T>` method returns the typed object.

## Format settings

Calling `HorseJsonFlow` (no charset argument) also sets `TJsonFlow.FormatSettings` to `en-US` locale — ensuring decimal separator is `.` for all JSON operations in the application.

## Global `TJsonFlow.FormatSettings`

```delphi
uses
  JsonFlow;
  SysUtils;

var
  LFS: TFormatSettings;
begin
  LFS := TFormatSettings.Create('en-US');
  TJsonFlow.FormatSettings := LFS;
end;
```

This affects the shared writer/reader/builder used by the `TJsonFlow` facade.

:::warning
`THorseRequestHelper.Body<T>` returns a new object — the caller is responsible for freeing it. In Horse handlers the convention is to free after the response is sent (or use a `try/finally`).
:::

:::note Horse installation
```sh
boss install HashLoad/horse
```
`Horse.JsonFlow` adds `Source\Middleware-Horse\` to the search path. Make sure both JsonFlow and Horse search paths are included in your `.dproj`.
:::
