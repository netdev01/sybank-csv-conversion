# ✅ Instruction: Convert Danish CSV Bank Data to Standardized US Format

## Important Note on CSV Format

All exported files from Sydbank use **semicolon (`;`) as the delimiter**, not comma.  
When loading these files in any programmatic environment (e.g., Python with `pandas`), you must specify:

```python
pd.read_csv("file.csv", delimiter=";")
```

Otherwise, the file may fail to load or be incorrectly parsed into one column.

## 🔧 Goal
Convert one or more Danish-formatted CSV files into a standardized CSV with:
- Translated headers
- Reformatted dates and numbers
- Cleaned encoding
- Selected only these columns: *Date, Payee, *Amount, Description, Reference. Date and Amount need a `*` prefix.
- Output file name must follow file conversion: original file name + "-converted" suffix.

## 🔄 Updated Conversion Flow (User Requirement)

- ✅ Convert **only one file first** and display the result.
- ✅ **After previewing**, ask the user if they want to convert the remaining uploaded files.
- ✅ Once all files are converted, ask the user if they want to export all files as downloadable CSVs.
- ❌ Do **not** batch convert or download all files immediately without user prompts.

## 📥 Step 1: Upload CSV Files
Upload any bank-exported CSV file that:
- Uses **`;`** as delimiter
- Is encoded in **ISO-8859-1**
- Contains Danish-style numbers and dates

## 📑 Step 2: Parse and Clean Columns

Clean and rename these columns:

| Original Column Name         | New Name     | Notes                                                                 |
|-----------------------------|--------------|-----------------------------------------------------------------------|
| `ï»¿Dato` or `Dato`         | `*Date`      | Convert `DD.MM.YYYY` → `MM/DD/YYYY`                                   |
| `Tekst`                     | `Payee`      | Leave as-is                                                           |
| `Beløb` or `BelÃ¸b`         | `*Amount`    | Convert from Danish number format: `"14.879,62"` → `"14879.62"`       |
| `Hovedkategori`             | `Description`| Decode to fix characters like `LÃ¥n og gÃ¦ld` → `Lån og gæld`        |
| `Kategori` or `Underkategori`| `Reference` | Also decode for encoding issues                                       |

> Note: Use `Hovedkategori` and `Kategori` when `Underkategori` is missing.


## 🧠 Step 3: Apply Transformations

### ✅ Encoding
Read file with `ISO-8859-1` encoding.

### ✅ Delimiter
Use `;` as delimiter.

### ✅ Date Format
Convert `*Date` from `DD.MM.YYYY` to `MM/DD/YYYY`.

### ✅ Number Format
Convert `*Amount`:
- Remove thousand separators (`.`)
- Convert decimal `,` to `.`
- Example: `"14.879,62"` → `"14879.62"`

If you want to match comma-decimal style later, just convert `.` back to `,`.

### ✅ Fix Encoding
Fix columns like `Description` and `Reference`:
```python
df["Description"] = df["Description"].str.encode("ISO-8859-1").str.decode("utf-8")
df["Reference"] = df["Reference"].str.encode("ISO-8859-1").str.decode("utf-8")
```

## 👁️ Step 4: Preview First File Only

Before processing all files:

1. Convert only one uploaded CSV file.
2. Display the first few rows of the converted data to the user.
3. Wait for explicit approval before proceeding with the rest.

## 💾 Step 5: Save the Output
Save the final CSV file with:
- Comma `,` as delimiter
- Encoding: `ISO-8859-1`
- Columns:  
  `*Date`, `Payee`, `*Amount`, `Description`, `Reference`

```python
df.to_csv("filename_transformed.csv", index=False, encoding="ISO-8859-1")
```

## ✅ Example Output Columns

| *Date     | Payee                       | *Amount | Description      | Reference             |
|-----------|-----------------------------|---------|------------------|------------------------|
| 07/31/2024| Fra EUR-konto               | 14879.62| Indtægter        | Anden indtægt         |
| 07/01/2024| AIB MERCHANT SERVICES       | 1349.57 | Indtægter        | Anden indtægt         |
| 06/28/2024| Rente                       | 5.61    | Lån og gæld      | Renter og gebyrer     |

## 🛠️ Additional Notes for AI

To ensure proper parsing, match these exact header names **as they appear in ISO-8859-1 encoded files**:

| Header in File     | Mapped Name | Notes                                      |
|--------------------|-------------|--------------------------------------------|
| `ï»¿Dato` or `Dato`| `*Date`     | Convert `DD.MM.YYYY` → `MM/DD/YYYY`        |
| `Tekst`            | `Payee`     | Leave as-is                                |
| `BelÃ¸b`           | `Amount`    | Convert from Danish number format          |

- Files use `;` as a delimiter.
- Files are encoded with `ISO-8859-1` and may contain a BOM (Byte Order Mark).
- Do not rely on ideal Danish spellings; use actual header strings (`BelÃ¸b` instead of `Beløb`).
- Skip malformed rows silently.

## 📝 File Naming Convention

Save each converted file using the original filename, appending the word **"_converted"** before the extension. For example:

- Input: `EUR-konto_2025-03-01-2025-06-30.csv`
- Output: `EUR-konto_2025-03-01-2025-06-30-converted.csv`

Ensure output files are encoded as `UTF-8` with comma-separated values.
