# JsonFlow / JsonFlow4D for Delphi

[![Delphi Supported Versions](https://img.shields.io/badge/Delphi%20Supported%20Versions-XE%2B-blue.svg)]()
[![License](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

*   [🇬🇧 English](#-english)
*   [🇧🇷 Português](#-português)

---

## 🇬🇧 English

**JsonFlow** (internally declared as **JsonFlow4D**) is a state-of-the-art, high-performance, and feature-rich JSON manipulation, serialization, and JSON Schema validation framework for Delphi. 

It provides an enterprise-grade toolkit that integrates high-speed serialization, persistent caches, multi-threaded validation, and dynamic JSON reading/writing under a unified, fluent API.

### 🏛 Supported Platforms
*   **Delphi XE or superior** (VCL, FMX, Console, Multi-Threaded)
*   **Lazarus / FreePascal** (Compatible Core)

---

### 🚀 Key Features

*   **Advanced Serialization & Deserialization:** Automatic conversion between Delphi objects and JSON structures using custom attributes, mapping rules, and extensible serialization pipelines.
*   **Dynamic JSON Reader & Writer:** Modifies JSON structures in-place. Traverse nested elements fluidly using path strings (e.g., `user.address[0].zip`).
*   **Draft 7 JSON Schema Validation:** Complete validation of JSON structures against JSON Schema Draft 7 specifications with detailed local error paths (`Path` and `SchemaPath`).
*   **Optimized Native Performance:**
    *   **Navigation Cache:** Up to 2.5x speed improvements for repeating paths.
    *   **Batch Mode:** Up to 3.4x faster operations for bulk updates.
    *   **Object Pooling:** Multi-threaded pooled composers yielding up to 3.0x faster creation/destruction cycles.
*   **Extensible Middleware System:** Customize serialization pipelines, encrypt sensitive fields, or format custom structures (e.g., CPF, CNPJ, dates) on the fly.
*   **Advanced Observability & Metrics:** High-precision logging, metrics, and multi-format validation reporting (HTML, XML, CSV, PDF).
*   **Asynchronous Validation:** Multi-threaded thread-safe background validator with priority queues.

---

### ⚡️ Quick Start

#### 1. Automatic Object Serialization
```delphi
uses
  JsonFlow.Serializer,
  JsonFlow.Interfaces;

var
  LSerializer: TJSONSerializer;
  LJson: string;
  LUser: TUser;
begin
  LSerializer := TJSONSerializer.Create;
  try
    LUser := TUser.Create;
    LUser.Name := 'John Doe';
    LUser.Age := 30;
    
    // Object to JSON String
    LJson := LSerializer.ObjectToJSON(LUser);
    
    // JSON String back to Object
    LUserCopy := LSerializer.JSONToObject<TUser>(LJson);
  finally
    LSerializer.Free;
  end;
end;
```

#### 2. In-Place Dynamic Reading & Updating (Composer)
```delphi
uses
  JsonFlow.Composer;

var
  LComposer: TJSONComposer;
  LJsonInput, LUpdatedJson: string;
begin
  LJsonInput := '{"user":{"name":"John","age":30},"tags":["dev"]}';
  
  LComposer := TJSONComposer.Create;
  try
    LComposer.LoadJSON(LJsonInput);
    
    // Enable performance optimizations
    LComposer.EnableCache(1000);
    LComposer.BeginBatch;
    try
      LComposer.SetValue('user.age', 31); // Update age
      LComposer.AddToArray('tags', 'lead'); // Add tag
      LComposer.SetValue('user.email', 'john@email.com'); // Insert new key
    finally
      LComposer.EndBatch;
    end;
    
    LUpdatedJson := LComposer.Generate;
  finally
    LComposer.Free;
  end;
end;
```

#### 3. Asynchronous Draft 7 JSON Schema Validation
```delphi
uses
  JsonFlow.SchemaValidator,
  JsonFlow.Interfaces;

var
  LValidator: TSchemaValidator;
  LSchema, LData: IJSONElement;
  LErrors: TList<TValidationError>;
begin
  LValidator := TSchemaValidator.Create;
  try
    LSchema := TJSONElement.ParseFromString('{ "type": "object", "properties": { "name": {"type": "string", "minLength": 2} }, "required": ["name"] }');
    LData := TJSONElement.ParseFromString('{ "name": "A" }'); // Too short!
    
    LValidator.Schema := LSchema;
    LErrors := LValidator.Validate(LData);
    
    if LErrors.Count > 0 then
      WriteLn('Validation failed on path: ' + LErrors[0].Path);
  finally
    LValidator.Free;
  end;
end;
```

---

### ⛏️ Contributing
Issues and pull requests are welcome. Feel free to fork the repository and submit your changes.

### 📬 Contact & Support
*   **Telegram**: [HashLoad Channel](https://t.me/hashload)
*   **Website**: [isaquepinheiro.com.br](https://www.isaquepinheiro.com.br)

---

## 🇧🇷 Português

**JsonFlow** (declarado internamente como **JsonFlow4D**) é um framework de manipulação, serialização e validação de JSON Schema Draft 7 de alto desempenho e rico em recursos para Delphi.

Ele oferece uma caixa de ferramentas corporativa que integra serialização de alta velocidade, caches persistentes, validação multi-thread e leitura/escrita dinâmica de JSON sob uma API fluente e unificada.

---

### 🚀 Recursos Principais

*   **Serialização e Deserialização Avançada:** Conversão automática entre objetos Delphi e strings JSON utilizando atributos personalizados, regras de mapeamento inteligentes e pipelines de processamento.
*   **Leitura e Escrita Dinâmica de JSON (Composer):** Permite ler e modificar dados JSON in-place em tempo de execução de forma encadeável usando caminhos intuitivos (ex: `usuario.endereco[0].cep`).
*   **Validação Completa de JSON Schema Draft 7:** Validação robusta de JSONs contra especificações Draft 7 com gravação detalhada de caminhos dos erros (`Path` e `SchemaPath`).
*   **Melhorias Nativas de Performance:**
    *   **Cache de Navegação:** Até 2.5x mais rápido para rotas repetitivas.
    *   **Operações em Lote (Batch):** Até 3.4x mais rápido para atualizações em massa.
    *   **Pool de Objetos:** TPooledJSONComposer nativo multi-thread proporcionando até 3.0x mais velocidade em loops intensivos.
*   **Sistema de Middlewares Extensível:** Customize serializações de forma integrada, criptografe campos sensíveis no fluxo de dados ou formate datas de forma global.
*   **Alta Observabilidade:** Rastreamento de latência e hit rate de caches, com geração de relatórios de validação estruturados (HTML com gráficos, PDF, CSV, XML, JSON).
*   **Validação Assíncrona:** Fila de validação thread-safe rodando em background com controle de prioridades.

---

### ⚡️ Início Rápido

#### 1. Serialização Automática de Objetos
```delphi
uses
  JsonFlow.Serializer,
  JsonFlow.Interfaces;

var
  LSerializer: TJSONSerializer;
  LJson: string;
  LUser: TUser;
begin
  LSerializer := TJSONSerializer.Create;
  try
    LUser := TUser.Create;
    LUser.Name := 'João Silva';
    LUser.Age := 30;
    
    // Objeto para String JSON
    LJson := LSerializer.ObjectToJSON(LUser);
    
    // String JSON de volta para Objeto
    LUserCopy := LSerializer.JSONToObject<TUser>(LJson);
  finally
    LSerializer.Free;
  end;
end;
```

#### 2. Edição Dinâmica e Otimizada de JSON (TJSONComposer)
```delphi
uses
  JsonFlow.Composer;

var
  LComposer: TJSONComposer;
  LJsonInput, LUpdatedJson: string;
begin
  LJsonInput := '{"usuario":{"nome":"João","idade":30},"tags":["dev"]}';
  
  LComposer := TJSONComposer.Create;
  try
    LComposer.LoadJSON(LJsonInput);
    
    // Ativa otimizações nativas
    LComposer.EnableCache(1000);
    LComposer.BeginBatch;
    try
      LComposer.SetValue('usuario.idade', 31); // Atualiza idade
      LComposer.AddToArray('tags', 'lead'); // Adiciona tag
      LComposer.SetValue('usuario.email', 'joao@email.com'); // Insere nova chave
    finally
      LComposer.EndBatch;
    end;
    
    LUpdatedJson := LComposer.Generate;
  finally
    LComposer.Free;
  end;
end;
```

#### 3. Validação de JSON Schema Draft 7
```delphi
uses
  JsonFlow.SchemaValidator,
  JsonFlow.Interfaces;

var
  LValidator: TSchemaValidator;
  LSchema, LData: IJSONElement;
  LErrors: TList<TValidationError>;
begin
  LValidator := TSchemaValidator.Create;
  try
    LSchema := TJSONElement.ParseFromString('{ "type": "object", "properties": { "nome": {"type": "string", "minLength": 2} }, "required": ["nome"] }');
    LData := TJSONElement.ParseFromString('{ "nome": "J" }'); // Nome curto demais!
    
    LValidator.Schema := LSchema;
    LErrors := LValidator.Validate(LData);
    
    if LErrors.Count > 0 then
      WriteLn('Validação falhou no path: ' + LErrors[0].Path);
  finally
    LValidator.Free;
  end;
end;
```

---

### ⛏️ Contribuição
Adoramos contribuições! Sinta-se à vontade para abrir issues ou enviar pull requests.

### 📬 Contato & Suporte
*   **Telegram**: [Canal HashLoad](https://t.me/hashload)
*   **Website**: [isaquepinheiro.com.br](https://www.isaquepinheiro.com.br)

---
*Copyright © 2025-2026 Isaque Pinheiro. Licensed under MIT License.*
