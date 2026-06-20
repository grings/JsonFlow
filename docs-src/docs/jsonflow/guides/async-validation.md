---
title: Async validation
sidebar_position: 9
---

# Async validation

`TAsyncValidator` (unit `JsonFlow.AsyncValidator`) provides a multi-threaded, thread-safe background queue for validating large batches of JSON documents against a Draft 7 schema.

## Overview

- Validation tasks are submitted to a queue and executed in parallel worker threads.
- Each task has a configurable `TPriority` (Low, Normal, High, Critical).
- Completion and progress are reported via callbacks.

## Basic usage

```delphi
uses
  JsonFlow.AsyncValidator,
  JsonFlow.Interfaces;

var
  LValidator: TAsyncValidator;
  LTaskId: string;
begin
  LValidator := TAsyncValidator.Create;
  try
    // Configure (optional — defaults are sensible)
    // <!-- TODO: confirm TAsyncValidator.Config property name -->

    // Submit a validation task
    LTaskId := LValidator.Submit(
      '{"name":"Alice"}',           // JSON data as string
      '{"type":"object","required":["name"]}', // schema as string
      TAsyncValidator.TPriority.Normal
    );  // <!-- TODO: confirm Submit method signature -->

    // Wait for all tasks
    LValidator.WaitAll; // <!-- TODO: confirm WaitAll method name -->
  finally
    LValidator.Free;
  end;
end;
```

## Task status

```delphi
TAsyncValidator.TStatus = (
  Queued,     // Task is waiting to be picked up by a worker thread
  Running,    // Task is currently being validated
  Completed,  // Validation finished (IsValid set accordingly)
  Cancelled,  // Task was cancelled before execution
  Error       // An exception occurred during validation
);
```

## TResult record

```delphi
TAsyncValidator.TResult = record
  TaskId: string;
  Status: TStatus;
  IsValid: Boolean;
  Errors: TList<TValidationError>;
  StartTime: TDateTime;
  EndTime: TDateTime;
  ErrorMessage: string;
end;
```

## Priority

```delphi
TAsyncValidator.TPriority = (
  Low,
  Normal,
  High,
  Critical
);
```

Higher-priority tasks are processed before lower-priority tasks in the queue when `EnablePrioritization` is `True` in `TConfig`.

## TConfig

| Field | Type | Default | Description |
|---|---|---|---|
| `MaxThreads` | `Integer` | <!-- TODO: confirm --> | Maximum parallel worker threads |
| `QueueCapacity` | `Integer` | <!-- TODO: confirm --> | Maximum queued tasks |
| `TaskTimeoutSeconds` | `Integer` | <!-- TODO: confirm --> | Per-task timeout |
| `EnablePrioritization` | `Boolean` | `True` | Priority-queue ordering |
| `EnableLoadBalancing` | `Boolean` | `True` | Distribute load across threads |
| `ThreadIdleTimeoutSeconds` | `Integer` | <!-- TODO: confirm --> | Idle thread shutdown time |

## Callbacks

```delphi
// Called when a task completes
LValidator.OnCompleted :=
  procedure(const AResult: TAsyncValidator.TResult)
  begin
    if AResult.IsValid then
      WriteLn(AResult.TaskId + ': valid')
    else
      WriteLn(AResult.TaskId + ': INVALID (' + IntToStr(AResult.Errors.Count) + ' errors)');
  end;

// Called with progress updates
LValidator.OnProgress :=
  procedure(const ATaskId: string; AProgress, ATotal: Integer)
  begin
    WriteLn(ATaskId + ': ' + IntToStr(AProgress) + '/' + IntToStr(ATotal));
  end;
```

<!-- TODO: confirm OnCompleted and OnProgress property names from TAsyncValidator -->

:::note
`TList<TValidationError>` in `TResult.Errors` is owned by the result. Callers must free it when done if they hold a reference to the result record outside the callback scope.
:::
