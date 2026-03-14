# OpenAgent SDK for Go

Parse, validate, and work with [`agent.yaml`](https://github.com/openagent-spec/spec) manifests in Go.

## Install

```bash
go get github.com/openagent-spec/sdk-go
```

## Usage

```go
package main

import (
    "fmt"
    "log"

    openagent "github.com/openagent-spec/sdk-go"
)

func main() {
    // Parse from file
    m, err := openagent.ParseFile("agent.yaml")
    if err != nil {
        log.Fatal(err)
    }

    fmt.Printf("%s v%s — %s\n", m.Name, m.Version, m.Description)
    fmt.Printf("Framework: %s\n", m.PreferredFramework())
    fmt.Printf("Required tools: %v\n", m.RequiredTools())
}
```

### Parse from bytes

```go
data := []byte(`
id: "my-agent"
name: "My Agent"
version: "1.0.0"
description: "A helpful assistant"
persona:
  style: "friendly"
  tone: "professional"
`)

m, err := openagent.Parse(data)
```

### Experience Packs

```go
// Extract from MEMORY.md
content, _ := os.ReadFile("MEMORY.md")
packs := openagent.MemoryToExperiences(string(content))

// L1 sanitize
for i := range packs {
    openagent.SanitizeExperiencePack(&packs[i])
}

// L2 AI refinement
cfg := openagent.L2RefineConfig{
    BaseURL: "https://api.openai.com",
    APIKey:  os.Getenv("OPENAI_API_KEY"),
    Model:   "gpt-4o",
}
openagent.RefineL2(&packs[0], cfg)
```

### Sanitize text

```go
text := "Contact admin@company.com at 192.168.1.100"
sanitized, redactions := openagent.SanitizeL1Text(text)
// sanitized: "Contact [email] at [ip]"
// redactions: ["emails", "ipv4"]
```

## API

| Function | Description |
|----------|-------------|
| `Parse(data []byte)` | Parse agent.yaml from bytes |
| `ParseFile(path string)` | Parse agent.yaml from file |
| `ParseExperiencePack(data []byte)` | Parse a single experience pack |
| `ParseExperienceDir(dir string)` | Parse all packs in a directory |
| `MemoryToExperiences(content string)` | Extract experience packs from MEMORY.md |
| `SanitizeL1Text(text string)` | Apply L1 regex sanitization |
| `SanitizeExperiencePack(pack)` | L1 sanitize all fields of a pack |
| `RefineL2(pack, config)` | AI-assisted L2 refinement |
| `GenerateExperienceID(domain, summary)` | Deterministic experience ID |

## Spec

Full specification: [github.com/openagent-spec/spec](https://github.com/openagent-spec/spec)

## License

MIT
