# csv

[![CI](https://github.com/alya-lang/csv/actions/workflows/ci.yml/badge.svg)](https://github.com/alya-lang/csv/actions/workflows/ci.yml)
[![License](https://img.shields.io/github/license/alya-lang/csv?color=blue&label=License)](LICENSE)
[![Alya](https://img.shields.io/badge/dynamic/toml?url=https%3A%2F%2Fraw.githubusercontent.com%2Falya-lang%2Fcsv%2Fmain%2Falya.toml&query=%24.package.alya-version&label=Alya&color=orange&prefix=%3E%3D)](https://github.com/alya-lang/alya)
[![Package Version](https://img.shields.io/badge/dynamic/toml?url=https%3A%2F%2Fraw.githubusercontent.com%2Falya-lang%2Fcsv%2Fmain%2Falya.toml&query=%24.package.version&label=Version&color=brightgreen)](alya.toml)

RFC-4180 compliant CSV and TSV parser, serializer, and data processor for Alya.

---

## 🌟 Features

- ⚡ **High Performance**: Native parsing and stringification reaching over 1M ops/sec.
- 📜 **RFC-4180 Compliant**: Handles commas inside quotes, CRLF/LF line endings, escaped double quotes (`""`), and multiline fields.
- 📑 **Header-to-Map Records**: Automatically maps header rows into structured dictionary records (`[ {"name": "Alice", "role": "Dev"} ]`).
- 🔄 **Custom Delimiters**: Built-in support for CSV (`,`), TSV (`\t`), Semicolon (`;`), and custom delimiters.
- 📁 **File I/O**: Direct helpers to read and write rows or records to files.
- 📦 **Zero Dependencies**: Pure Alya code, fully self-contained.

---

## 📁 Project Architecture

```
csv/
├── alya.toml               # Package manifest
├── src/
│   ├── lib.alya            # Public API facade
│   ├── types.alya          # CsvOptions configuration struct & constructors
│   ├── core/
│   │   ├── parser.alya     # State-machine RFC-4180 CSV / TSV parser & record mapper
│   │   └── writer.alya     # CSV / TSV serializer & field escaping
│   └── io/
│       └── file.alya       # File read / write operations
├── examples/
│   └── demo.alya           # Comprehensive runnable demo
├── tests/
│   └── test_basic.alya     # 47 RFC-4180 automated test cases
└── benches/
    └── bench_basic.alya    # Micro-benchmarks
```

---

## 📦 Installation

Add `csv` to the `[dependencies]` section in your `alya.toml`:

```toml
[dependencies]
csv = { git = "https://github.com/alya-lang/csv", tag = "v0.1.0" }
```

Or install it directly using the Alya package CLI:

```bash
alyac add csv --git https://github.com/alya-lang/csv --tag v0.1.0
alyac install
```

---

## 🚀 Quick Start

### 1. Basic Parsing & Stringification

```alya
import "csv"

function main()
    let raw = "name,role,city\nAlice,Engineer,\"New York, NY\"\nBob,Lead,London"

    # Parse into 2D row array
    let rows = csv::parse_csv(raw)
    say "First person: " + rows[1][0] # Alice

    # Serialize back to CSV
    let output = csv::stringify_csv(rows)
    say output
end

main()
```

### 2. Working with Map Records

```alya
import "csv"

function main()
    let raw = "id,product,price\n101,Laptop,1200\n102,Mouse,25"
    let records = csv::parse_csv_records(raw)

    let i = 0
    while i < len(records)
        let item = records[i]
        say "Item: " + item["product"] + " -> $" + item["price"]
        i += 1
    end

    # Serialize records with specific column order
    let headers = ["id", "product", "price"]
    let csv_text = csv::stringify_csv_records(records, headers)
    say csv_text
end

main()
```

### 3. File Operations

```alya
import "csv"

function main()
    let path = "./inventory.csv"
    let rows = [
        ["sku", "name", "qty"],
        ["A01", "Keyboard", "50"],
        ["B02", "Monitor", "20"]
    ]

    # Write rows to file
    csv::write_file_rows(path, rows)

    # Read rows back
    let loaded = csv::read_file_rows(path)
    say "Total rows: " + str(len(loaded))
end

main()
```

---

## 📖 API Reference

### Options Constructors

| Function | Arguments | Returns | Description |
|---|---|---|---|
| `options()` | - | `CsvOptions` | Returns default CSV options (`,` delimiter, CRLF disabled, trim disabled). |
| `tsv_options()` | - | `CsvOptions` | Returns TSV options (`\t` delimiter). |
| `custom_options(delimiter)` | `delimiter: string` | `CsvOptions` | Returns options with a custom delimiter character. |

### Parsing Functions

| Function | Arguments | Returns | Description |
|---|---|---|---|
| `parse_csv(text, opts = null)` | `text: string, opts: CsvOptions` | `array` | Parses CSV text into a 2D array of rows. |
| `parse_tsv(text, opts = null)` | `text: string, opts: CsvOptions` | `array` | Parses TSV text into a 2D array of rows. |
| `parse_csv_records(text, opts = null)` | `text: string, opts: CsvOptions` | `array` | Parses CSV text into a list of Map records using the header row. |
| `parse_tsv_records(text, opts = null)` | `text: string, opts: CsvOptions` | `array` | Parses TSV text into a list of Map records using the header row. |

### Serialization Functions

| Function | Arguments | Returns | Description |
|---|---|---|---|
| `stringify_csv(rows, opts = null)` | `rows: array, opts: CsvOptions` | `string` | Serializes a 2D array of rows into CSV text. |
| `stringify_tsv(rows, opts = null)` | `rows: array, opts: CsvOptions` | `string` | Serializes a 2D array of rows into TSV text. |
| `stringify_csv_records(records, headers = null, opts = null)` | `records: array, headers: array, opts: CsvOptions` | `string` | Serializes Map records into CSV text with headers. |
| `stringify_tsv_records(records, headers = null, opts = null)` | `records: array, headers: array, opts: CsvOptions` | `string` | Serializes Map records into TSV text with headers. |

### File I/O Functions

| Function | Arguments | Returns | Description |
|---|---|---|---|
| `read_file_rows(path, opts = null)` | `path: string, opts: CsvOptions` | `array` | Reads and parses rows directly from a CSV file. |
| `read_file_records(path, opts = null)` | `path: string, opts: CsvOptions` | `array` | Reads and parses Map records directly from a CSV file. |
| `write_file_rows(path, rows, opts = null)` | `path: string, rows: array, opts: CsvOptions` | `int` | Serializes rows and writes to a CSV file. |
| `write_file_records(path, records, headers = null, opts = null)` | `path: string, records: array, headers: array, opts: CsvOptions` | `int` | Serializes Map records and writes to a CSV file. |

---

## 🧪 Running Tests & Benchmarks

Run the test suite using `alyac`:

```bash
alyac test
# or
alyac run tests/test_basic.alya
```

Run the benchmark suite:

```bash
alyac run benches/bench_basic.alya
```

Run the example demo:

```bash
alyac run examples/demo.alya
```

---

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.