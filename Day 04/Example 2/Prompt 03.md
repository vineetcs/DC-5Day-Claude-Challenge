```text



The available columns should not take too much height and width. It should be simple text in a comma-separated format, and that itself is enough.

Update the application to handle different timeframes for the sales targets. The uploaded invoice data represents a full quarter (3 months) of sales, but the target data might be provided on a monthly basis.
Please add a UI element (such as a dropdown or radio button toggle) next to the Target Data upload section asking the user: 'Are these targets Monthly or Quarterly?'.
Modify the calculation logic based on this selection:
If the user selects 'Quarterly', use the target amounts from the file exactly as they are.
If the user selects 'Monthly', automatically multiply the target amounts by 3 to derive the quarterly target before comparing them against the total quarterly sales from the invoice data.
Ensure this logic seamlessly integrates with the dynamic column mapping from the previous step, and that the top achievers leaderboard, individual progress charts, and 'gap to next tier' calculations update instantly based on the selected target frequency.



```

