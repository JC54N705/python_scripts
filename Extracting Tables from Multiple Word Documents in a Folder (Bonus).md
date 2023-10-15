```python
# Input and validate the filename
def input_filename():

    # Prompt the user to enter the filename (without the file extension)
    excel_filename = input("Enter Excel filename (without extension): ")

    # Validade the Excel filename
    if not excel_filename:
        raise ValueError("Excel filename cannot be empty.")   
    if any(c in r"\/:*?\"<>|" for c in excel_filename):
        raise ValueError("Excel filename contains invalid characters.")

    return excel_filename + ".xlsx" #  Add the ".xlsx" extension

# Save the DataFrame to Excel at the specified location
def save_to_excel(dataframe, directory_path):

    # Check if the specified directory path exists
    if not os.path.exists(directory_path):
        raise FileNotFoundError(f"Directory path not found.")

    # Retrieve the Excel filename from the user
    excel_filename = input_filename()

    # Create the full output path by combining the directory and filename
    output_path = os.path.join(directory_path, excel_filename)

    # Save the DataFrame to Excel at the specified location
    dataframe.to_excel(output_path, index=False)
    print(f"Excel file saved at: {output_path}")

# Define the directory where the Excel file will be saved
directory_path = r"C:\Users\Utilizador\Desktop"

# Call the function to save the DataFrame to Excel
save_to_excel(df, directory_path)
```