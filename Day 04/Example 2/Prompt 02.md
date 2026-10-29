```text



Update the application's CSV parsing logic to completely remove strict column name dependencies. Instead of looking for specific normalized headers, implement a dynamic keyword-based column mapping system.

The application should scan the incoming CSV headers and use common business keywords or fuzzy matching to automatically identify and categorize the columns into standard required roles. Use the following logic to classify columns regardless of their exact naming convention:

Invoice Number: Look for keywords like 'invoice', 'inv', 'no', 'number', 'id', 'ref'.

Invoice Date: Look for keywords like 'date', 'time', 'period', 'timestamp'.

Customer Name: Look for keywords like 'customer', 'client', 'company', 'account', 'buyer'.

Sales Rep: Look for keywords like 'rep', 'salesperson', 'employee', 'staff', 'agent'.

Sales/Net Amount: Look for keywords like 'sales', 'net', 'amount', 'revenue', 'total', 'price', 'value'.

Cost Column: Look for keywords like 'cost', 'cogs', 'expense', 'purchase'.

Profit Column: Look for keywords like 'profit', 'margin', 'gain'.

Modify the JavaScript so that it checks headers against these keyword lists (ignoring case, spaces, and special characters) and maps the best match to each role. The application must proceed with the quarterly commission calculations using these dynamically identified columns. Under no circumstances should the app fail or throw an error stating that exact column names (like 'customer name') are missing. It must intelligently deduce the columns based on these common sales terms.



```

