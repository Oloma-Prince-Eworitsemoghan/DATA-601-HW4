**HOW REGEX WAS USED IN THIS ASSIGNMENT**

1.Extracting Numeric Values from Text
Some records contained text-based numeric values, requiring extraction before converting them into numbers. To extract only valid numeric portions from such entries, this Regex extraction function was applied: 
# Extract numeric values from text-based numeric columns
numeric_columns = ["Property GFA - Self-Reported (m²)", "Site Energy Use (GJ)", "Total GHG Emissions (Metric Tons CO2e)"]
def extract_numeric(value):
    if isinstance(value, str):
        match = re.search(r"[\d,]+\.?\d*", value)
        if match:
            return float(match.group().replace(",", ""))
    return value

for col in numeric_columns:
    data_cleaned[col] = data_cleaned[col].apply(extract_numeric).astype(float)
    # Display extracted numeric values for verification
print(data_cleaned[["Property GFA - Self-Reported (m²)", "Site Energy Use (GJ)", "Total GHG Emissions (Metric Tons CO2e)"]].head())

Match = re.search(r”[\d, ]+\.?\d*”, value) - This pattern captures whole numbers, decimals, comma-seperated values.
return float(match.group().replace(",", "")) - This removes commas before converting the values to float.

2. Standardizing Postal Codes
Canadian postal codes follow the A1A 1A1 format (e.g., "T2M 2M5"). However, there were inconsistencies such as, Missing spaces (e.g., "T2P2M5") and lowercase letters (e.g., "t2p 2m5")




# Standardize Postal Codes using Regex
def clean_postal_code(postal_code):
    match = re.match(r"([A-Z]\d[A-Z])\s*(\d[A-Z]\d)", postal_code.strip(), re.I)
    return f"{match.group(1).upper()} {match.group(2).upper()}" if match else postal_code

data_cleaned["Postal Code"] = data_cleaned["Postal Code"].apply(clean_postal_code)
# Display standardized postal codes
print(data_cleaned[["Postal Code"]].head())

 ([A-Z]\d[A-Z])\s*(\d[A-Z]\d) captures two postal code segments.

 \s* allows for optional spaces.
  
.upper() ensures all letters are capitalized.

**3.Identifying Invalid Numeric Values**
Some numeric columns (e.g., Property GFA, Site Energy Use, Total GHG Emissions) contained non-standard values like: Numbers with commas (e.g., "1,200" instead of 1200), Text mixed with numbers (e.g., "~500", "N/A"), Scientific notation (e.g., "4.5E+3"). 
def extract_numeric(value):
    if isinstance(value, str):
        match = re.search(r"[\d,]+\.?\d*", value)
        if match:
            return float(match.group().replace(",", ""))
    return value

 The pattern \d+(\.\d+)? ensures the value is a valid number (e.g., 123, 45.67).

·  .replace(",", "") removes commas to accommodate numbers formatted as "1,000".

. If a value does not match this pattern, it is flagged as invalid.


4.Clean and Extract meaningful text from property Names and Addresses

# Clean and extract meaningful text from Property Names and Addresses
def clean_text(value):
    if isinstance(value, str):
        return re.sub(r"[^a-zA-Z0-9\s]", "", value).strip()
    return value

data_cleaned["Property Name"] = data_cleaned["Property Name"].apply(clean_text)
data_cleaned["Address 1"] = data_cleaned["Address 1"].apply(clean_text)
print(data_cleaned[["Property Name", "Address 1"]].head())

 

 [^a-zA-Z0-9\s]
^ → Negation, meaning "not the following characters."
a-zA-Z → Allows uppercase and lowercase letters.
0-9 → Allows numbers.
\s → Allows whitespace (spaces, tabs, new lines).
·  This pattern removes anything that is NOT a letter, number, or space.
