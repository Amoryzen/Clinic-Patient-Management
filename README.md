# Clinic Patient Management (C/CSV, CLI + GTK)

Lightweight patient and treatment management for small clinics, with both terminal and GTK GUI interfaces, using CSV files for storage.

## Why This Exists

Small clinics and coursework projects often need a simple, offline way to track patient records, visits, diagnoses, and billing without introducing databases or complex stacks. This project provides a practical, CSV-backed solution that runs on Windows with minimal dependencies, plus a GUI option for a friendlier UX.

## Key Features

- Manage patient records: add, edit, delete, search
- Track medical visits: diagnosis, actions, follow-up, billing
- List action cost catalog from CSV
- Financial report: monthly/yearly totals and averages
- Disease analysis: counts per month/year by diagnosis
- Follow-up schedule info: list controls by date
- Two interfaces: interactive terminal and GTK-based GUI
- CSV persistence with straightforward, human-readable schemas

## Tech Stack

- Languages: C17
- Libraries:
  - CLI: C standard library only
  - GUI: GTK 4 (via glib2, pango, cairo, gdk-pixbuf, graphene)
- Build: MinGW GCC, MSYS2, pkg-config
- Storage: CSV files (semicolon-delimited)
- OS: Windows (tested with MSYS2 MinGW). Cross-platform likely with GTK 4 available.

Assumption: Windows + MSYS2 is the primary environment given the included VS Code configuration paths.

## Project Structure

- main_with_terminal.c — Terminal UI, CSV read/write, reports and analysis
- main_with_gui.c — GTK 4 GUI wiring to patient and visit operations
- my_functions.h/.c — Shared data types and operations used by the GUI
- Data Pasien.csv — Patient records (No;Nama Lengkap;Alamat;Kota;Tempat Lahir;Tanggal Lahir;Umur (th);No BPJS;ID Pasien)
- Riwayat Datang, Diagnosis, dan Tindakan.csv — Visit history (No;Tanggal;ID Pasien;Diagnosis;Tindakan;Kontrol;Biaya (Rp))
- Biaya Tindakan.csv — Procedure cost catalog (No;Aktivitas;Biaya)
- .vscode/ — Editor configs; a basic GCC build task for the active file

Note: CLI code duplicates some operations also present in my_functions.*; the GUI exclusively uses my_functions.*.

## Installation

- Install MSYS2 (64-bit) and update:
  
  ```bash
  pacman -Syu
  ```

- Install toolchain and GTK 4:

  ```bash
  pacman -S mingw-w64-x86_64-gcc mingw-w64-x86_64-pkg-config mingw-w64-x86_64-gtk4
  ```

- Verify pkg-config finds GTK:

  ```bash
  pkg-config --cflags --libs gtk4
  ```

- Optionally use VS Code with the provided settings; default task compiles the active C file with MinGW.

## Build

Run these in the MSYS2 MinGW64 shell from the repository directory.

- Terminal app:

  ```bash
  gcc -std=c17 -O2 -Wall -Wextra -o main_with_terminal.exe main_with_terminal.c my_functions.c
  ```

- GUI app (GTK 4):

  ```bash
  gcc -std=c17 -O2 -Wall -Wextra -o main_with_gui.exe main_with_gui.c my_functions.c $(pkg-config --cflags --libs gtk4)
  ```

Assumption: my_functions.c is compiled in both targets for consistency with existing instructions.

## Usage

- Prepare CSVs in the repo directory:
  - Data Pasien.csv header:

    ```text
    No;Nama Lengkap;Alamat;Kota;Tempat Lahir;Tanggal Lahir;Umur (th);No BPJS;ID Pasien
    ```

  - Riwayat Datang, Diagnosis, dan Tindakan.csv header:

    ```text
    No;Tanggal;ID Pasien;Diagnosis;Tindakan;Kontrol;Biaya (Rp)
    ```

  - Biaya Tindakan.csv header:

    ```text
    No;Aktivitas;Biaya
    ```

- Run terminal app:

  ```bash
  ./main_with_terminal.exe
  ```

  Example workflow:
  - Choose menu options 1–15 to list, add, modify, delete, search, report, analyze.
  - Edits persist by rewriting the CSV files on save or exit.

- Run GUI app:

  ```bash
  ./main_with_gui.exe
  ```

  Example workflow:
  - Use buttons for “Tambah/Ubah/Cari/Hapus Data Pasien”, “Riwayat Medis”, “Laporan Keuangan”, “Analisis”, “Informasi Kontrol”.
  - Results show inline; data persists to the same CSVs.

- Dates:
  - CLI analysis expects dates like “DD NamaBulan YYYY” with Indonesian month names (e.g., “12 Januari 2024”).
  - Follow-up search (“Kontrol”) matches exact strings in the Kontrol column.

## Configuration

- File names are hard-coded as “Data Pasien.csv” and “Riwayat Datang, Diagnosis, dan Tindakan.csv” in both main programs.
- To change locations or names, update string literals in main_with_terminal.c and main_with_gui.c, and ensure consistent headers across CSVs.
- Max in-memory records: 100 (MAX_PASIEN); increase the macro where defined if needed.

Assumption: No external configuration files or CLI flags are provided.

## Testing

- Manual tests:
  - Start with small CSVs (3–5 rows).
  - Exercise add/modify/delete/search in both CLI and GUI.
  - Verify persistence by reopening apps and inspecting CSV content.
  - Run financial report and disease analysis with mixed months/years.

- Static checks:
  - Compile with warnings enabled (-Wall -Wextra).
  - Consider running on Linux with valgrind to spot leaks; on Windows, use Dr. Memory or similar.

No automated unit test framework is included.

## Performance & Limitations

- In-memory limit: 100 records; reallocations occur in some report/analysis paths but inputs are capped.
- CSV parsing uses semicolon delimiters; malformed rows may cause incorrect parsing.
- No concurrent access or file locking; avoid running CLI and GUI simultaneously on the same CSVs.
- Date parsing relies on exact Indonesian month names; inconsistent formats will be ignored.
- Minimal input validation; user-provided fields are trusted.

## Roadmap

- Expand record capacity or switch to dynamic containers
- Improve validation and error handling
- Factor common operations to shared modules for CLI/GUI parity
- Add unit tests and CI
- Introduce SQLite as optional backend
- Cross-platform packaging (Windows installer, Flatpak on Linux)
- i18n for month names and labels

## Contributing

- Fork and create feature branches
- Keep C code at -std=c17; enable -Wall -Wextra, fix new warnings
- Prefer small, focused PRs with clear descriptions
- Update build instructions when touching GTK or toolchain requirements
- Add sample rows to CSVs when introducing new fields

## License

MIT License.

Assumption: License file not present; MIT chosen for permissive open-source usage. Update this section if a different license applies.
