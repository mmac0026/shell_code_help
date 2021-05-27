# csvkit

`csvkit` is a useful package for data handling and manipulation within the `bash` environment. 
It is a suite of command-line tools for converting to and working with CSV, the king of tabular file formats.

The package documentation can be found [here](https://csvkit.readthedocs.io/en/latest/).

For installation on MacOS, follow these instructions: _(you may require [HomeBrew](https://brew.sh/) to be installed)_

```bash
# Install csvkit for Python 3
pip3 install csvkit
```

If you come across errors during installation, visit the [tips and troubleshooting](https://csvkit.readthedocs.io/en/latest/tricks.html#troubleshooting) page of the csvkit documentation.

## in2csv

`in2csv` converts files into CSV format.
Currently, supported file types include CSV, DBF, FIXED, GEOJSON, JSON, NDJSON, XLS, and XLSX.


You can view the documentation for this command using:

```bash
in2csv -h
```

### Convert an XLSX document to CSV and save

The basic syntax for converting the file to CSV format is very simple, and requires only the XLSX file as input.
If you do not redirect the output to a new CSV file using `>`, it will simply be displayed on the console directly.

If the XLSX file you want to convert to a CSV file has multiple sheets, you can view the sheet names using the `-n` or `--names` flag without redirecting the output.
Once you know the name of the sheet you want to convert, you can append the `--sheet "{SHEET_NAME}"` flag to the end of your command and redirect the output.

```bash
# Convert a single-sheet XLSX file and save as a CSV file
in2csv example_file.xlsx > example_file.csv

# View the names of the sheets in your XLSX file
in2csv --names example_file2.xlsx

# Convert "Sheet_1" to CSV format and save
in2csv example_file2.xlsx --sheet "Sheet_1" > example_file2_sheet1.csv
```

## csvlook

`csvlook` renders a CSV to the command line in a Markdown-compatible, fixed-width format for easier viewing.

You can view the documentation for this command using:

```bash
csvlook -h
```

### Data preview on the command line

Most often, `csvlook` can be used with the CSV file name alone, but can be a useful tool to use at the end of a series of piped commands.

```bash
# Preview data using csvlook
csvlook example_file.csv
```
Output:
```
| track_id           | popularity |
| ------------------ | ---------- |
| 118GTY18274hasbf87 |          7 |
| 17GTSpt125SfbcT267 |          6 |
```

## csvstat

`csvstat` prints descriptive summary statistics on all columns in a CSV (e.g. mean, median, unique value counts etc.).

You can view the documentation for this command using:

```bash
csvstat -h
```

### Descriptive statistics on CSV data files

Similar to `csvlook`, often you will simply use `csvstat` with the file name.

```bash
# Get summary statistics on a CSV file
csvstat example_file.csv
```
Output:
```
1. "track_id"

  Type of data:         Text
  Contains null values: False
  Unique values:        24
  Longest value:        18 characters
  Most common values:   118GTY18274hasbf87 (1x)
                        17GTSpt125SfbcT267 (1x)
                        
2. "popularity"

  Type of data:         Number
  Contains null values: False
  Unique values:        7
  Smallest value:       1
  Largest value:        8
  Sum:                  122
  Mean:                 5.083
  Median:               5
  StDev:                2.141
  Most common values:   5 (6x)
                        4 (5x)
                        6 (5x)
```

## csvsql

`csvsql` applies SQL statements to one or more CSV files. 
It creates an in-memory SQL database that temporarily hosts the files being processed, and is therefore only suitable for small/medium-sized files.

You can view the documentation for this command using:

```bash
csvsql -h
```

### Applying SQL to a local CSV file

An example of the use of `csvsql` would be selecting every column from a given CSV file, and then viewing just the first row.

The `csvsql` command takes a SQL query argument and a CSV file as its arguments. 
The query must be a single line without breaks; a break via `\` can cause unexpected errors.

The output of `csvsql` can then be saved to an output file, or piped to further commands.

Additional documentation can be found by calling the command with the `-h` or `--help` flag.

```bash
# Print the first row (with row names) to the console
csvsql --query "SELECT * FROM example_file LIMIT 1" \
  example_file.csv

# Save the output to a new file
csvsql --query "SELECT * FROM example_file LIMIT 1" \
  example_file.csv > example_file_first1.csv

# Pipe the output to another command
csvsql --query "SELECT * FROM example_file LIMIT 1" \
  example_file.csv | csvlook
```
