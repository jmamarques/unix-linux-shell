#V1

 ```console
#!/bin/bash

input_file="large_file.dat"     # Replace with your input file name
output_folder="output_parts"    # Replace with the desired output folder
chunk_size=$((300*1024*1024))  # 300 MB in bytes

# Create the output folder if it doesn't exist
mkdir -p "$output_folder"

# Split the input file into parts
awk -v chunk_size="$chunk_size" 'BEGIN { part = 1; bytes_written = 0; } {
    if (bytes_written + length($0) > chunk_size) {
        close(output_file);
        part++;
        bytes_written = 0;
    }
    output_file = sprintf("%s/part_%d.dat", "'$output_folder'", part);
    print $0 > output_file;
    bytes_written += length($0);
}' "$input_file"

echo "Split $input_file into $(ls $output_folder | wc -l) parts."
 ```
 
#V2

 ```console
#!/bin/bash

input_file="large_file.dat"     # Replace with your input file name
output_folder="output_parts"    # Replace with the desired output folder
chunk_size=$((300*1024*1024))  # 300 MB in bytes

# Create the output folder if it doesn't exist
mkdir -p "$output_folder"

# Initialize variables
part=1
bytes_written=0
previous_line_start=""

# Function to close the current output file and start a new one
function close_output_file() {
    if [ -n "$output_file" ]; then
        close(output_file);
    fi
    part=$((part+1))
    bytes_written=0
}

# Read the input file line by line
while IFS= read -r line; do
    line_start="${line:0:100}"  # Use the first 100 characters as the expression

    # Check if the line starts with the same expression as the previous line
    if [ "$line_start" != "$previous_line_start" ]; then
        close_output_file
    fi

    # Open the output file if it's not already open
    if [ -z "$output_file" ]; then
        output_file="$output_folder/part_$part.dat"
    fi

    # Write the line to the current output file
    printf "%s\n" "$line" >> "$output_file"
    bytes_written=$((bytes_written+${#line}))

    # Update the previous line start
    previous_line_start="$line_start"

    # Check if the current output file exceeds the chunk size
    if [ "$bytes_written" -ge "$chunk_size" ]; then
        close_output_file
    fi

done < "$input_file"

# Close the last output file if it's still open
close_output_file

echo "Split $input_file into $(ls $output_folder | wc -l) parts."
 ```