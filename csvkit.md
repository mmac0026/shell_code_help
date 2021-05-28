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
> Output:
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
> Output:
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


## Filtering data

Using tools provided by `csvkit`, we can subset CSV file data via commands that target either columns or rows.

`csvcut`: filters data using __column__ name or position.

`csvgrep`: filters data by __row__ value through an exact match, pattern matching, or regex.

### csvcut

`csvcut` filters and truncates CSV files by column name or column position.

You can view the documentation for this command using:

```bash
csvcut -h
```

If you don't know the names or order of the columns in the document, you can use the `-n` or `--names` flag to print all column names.

```bash
# Return the names of the columns
csvcut -n example_file.csv
```
> Output:
```
1: track_id
2: popularity
```

The `-c` flag is used to select the columns you want to retain, either by using their names as a string, or by providing the column position.
Similar to R, column positions are indexed beginning at `1`, meaning that the first column is given a value of one (as opposed to Python, for example, which uses zero-based indexing).

```bash
# Return the first column in the data by position
csvcut -c 1 example_file.csv

# Return the first column in the data by name
csvcut -c "track_id" example_file.csv
```
> Output (identical in each case):
```
track_id
118GTY18274hasbf87
17GTSpt125SfbcT267
```

To select multiple columns, either names or positions can be given together, and separated using a comma (`,`).
There can be no spaces either side of the comma.

```bash
# Return the first and second columns in the data by position
csvcut -c 1,2 example_file.csv

# Return the first and second columns in the data by name
csvcut -c "track_id","popularity" example_file.csv
```
> Output (identical in each case):
```
track_id,popularity
118GTY18274hasbf87,7
17GTSpt125SfbcT267,6
```

### csvgrep

`csvgrep` filters by row using exact match or regex fuzzy matching, and must be paired with one of these options:

- `-m`: followed by the exact row value to filter
- `-r`: followed with a regex pattern
- `-f`: followed by the path to a file

You can view the documentation for this command using:

```bash
csvgrep -h
```

You can return an entire row of data by selecting the column to match with, using `-c`, and then inputing a term to match with `-m`.
The column can be defined either by name or position, just as with `csvcut`.

```bash
# Filter track_id for rows matching "118GTY18274hasbf87"
csvgrep -c "track_id" -m 118GTY18274hasbf87 example_file.csv

# Get the same result using column position
csvgrep -c 1 -m 118GTY18274hasbf87 example_file.csv
```
> Output (identical in each case):
```
track_id,popularity
118GTY18274hasbf87,7
```

Similarly, you could use regex patterns instead of an each match to filter for matches over multiple rows.

### csvstack

`csvstack` stacks up the rows from two or more CSV files. The columns of these files must be identical.

This is most useful when you have data that has been transferred in chunks, perhaps due to download/bandwidth limits.

You can view the documentation for this command using:

```bash
csvstack -h
```

You can first use `csvlook` to confirm that the data schema match between files.

If they do, the command simply takes two or more input CSV files, and can be redirected to a new file.
If you do not redirect it, the output will be displayed on the console.

```bash
# Stack two files together and output to a new file
csvstack example_file_1a.csv example_file_1b.csv > example_file_1_complete.csv

# Check the output looks as expected
csvlook example_file_1_complete.csv
```
> Output:
```
| track_id           | popularity |
| ------------------ | ---------- |
| 118GTY18274hasbf87 |          7 |
| 17GTSpt125SfbcT267 |          6 |
| 18Fg56Ash1hF980jbA |          4 |
| F8nas2nfasfKielA54 |          5 |
```

It may also be useful to know which file the data originated from.
This can be easily achieved using the `-g` flag, and specifying names for identification.

The new column name will be "group" by default, but can be customised by calling the `-n` flag, and specifying a new column name.

```bash
# Stack two files together, specify origin file names, and name the new column "source"
csvstack -g "file1a","file1b" -n "source" \
example_file_1a.csv example_file_1b.csv > example_file_1_complete.csv | csvlook
```
> Output:
```
| source | track_id           | popularity |
| ------ | ------------------ | ---------- |
| file1a | 118GTY18274hasbf87 |          7 |
| file1a | 17GTSpt125SfbcT267 |          6 |
| file1b | 18Fg56Ash1hF980jbA |          4 |
| file1b | F8nas2nfasfKielA54 |          5 |
```


## Handling data using SQL queries

### csvsql

`csvsql` applies SQL statements to one or more CSV files. 
It creates an in-memory SQL database that temporarily hosts the files being processed, and is therefore only suitable for small/medium-sized files.

You can view the documentation for this command using:

```bash
csvsql -h
```

#### Applying SQL to a local CSV file

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
