```python
import os
import pandas as pd
from docx import Document

# Extract a table by index from a Word document
def extract_table(document, table_index):

    # Check if the specified table index is out of bounds
    if table_index < 0 or table_index >= len(document.tables):
        raise IndexError(f"Table index {table_index} is out of bounds.")

    # Retrieve the table at the specified index
    table = document.tables[table_index]

    headers = None  # Variable to hold the column headers
    data = []       # Empty list to store the table data

    for i, row in enumerate(table.rows):
        # Extract the text from each cell in the table row
        text = (cell.text for cell in row.cells) 

        if i == 0:
            # Use the first row as headers
            headers = tuple(text)
            continue

        # Pair column headers with the cell values
        row_data = dict(zip(headers, text))

        # Append the data to the list
        data.append(row_data)

    return pd.DataFrame(data)  # Return as DataFrame

# Process the document for extraction
def process_document(doc_path):

    # Create the document object
    document = Document(doc_path)

    # Call the defined function to extract the table
    table = extract_table(document, table_index)
    
    # Get the filename from the document path
    filename = os.path.basename(doc_path)

    # Extract the customer reference from filename and create a new column
    table["Customer"] = os.path.splitext(filename)[0].split("-")[-1]

    return table  # Return the extracted table

# Concatenate tables from multiple files
def concatenate_tables(folder_path):

    frames = []  # List to store the extracted DataFrames

    # Check if the specified folder path exists
    if not os.path.exists(folder_path):
        raise FileNotFoundError(f"Folder '{folder_path}' does not exist.")

    total_files = 0      # Count the number of files in the folder
    processed_files = 0  # Count the files successfully processed

    # Iterate over the files in the specified folder
    for filename in os.listdir(folder_path):

        # Check for temp files within the folder
        if filename.startswith("~$"):
            raise ValueError(f"Close '{filename}' before running the script.")

        # Check if the file has a .docx extension
        if filename.endswith(".docx"):
          
            total_files += 1  # Increment the total files counter

            # Create the complete path to the iterated document
            doc_path = os.path.join(folder_path, filename)

            # Call the defined function and process the document
            table = process_document(doc_path)

            if table is not None:
                frames.append(table)  # Append the DataFrames to the list
                processed_files += 1  # Increment the processed files counter

    if frames:
      
        # Concat the DataFrames into a single DataFrame
        df = pd.concat(frames, ignore_index=True)
        
        # Move the specified column to the first position
        df.insert(0, 'Customer', df.pop('Customer'))

        # Return the resulting DataFrame and the processed file count
        print(f"Files processed: {processed_files} / {total_files}")
        return df, processed_files
    else:
        # Return no DataFrame and the processed file count
        print(f"No files were successfully processed.")
        return None, processed_files

# Define the directory path, folder name and table index
directory_path = r"C:\Users\Utilizador\Desktop"
folder_name = "Contracts"
table_index = 1

# Create the full folder path using the directory and folder names
folder_path = os.path.join(directory_path, folder_name)

# Call the defined function and capture the returned output
df, processed_files = concatenate_tables(folder_path)
```