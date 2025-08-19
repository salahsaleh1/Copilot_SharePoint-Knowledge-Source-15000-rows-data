# Copilot_SharePoint-Knowledge-Source-15000-rows-data
Copilot Agent to query SharePoint List Knowledge source with 15000 rows of data.


This Copilot Studio agent is designed to intelligently translate user questions about a library’s book catalog into precise OData $filter strings for querying a SharePoint list. It ensures natural language queries are converted into valid, structured expressions that match the schema of the catalog. By automatically handling text searches, numeric and date comparisons, and publisher name normalization, the agent helps users quickly find books without needing technical knowledge. It also supports combining multiple conditions to answer complex queries, making library searches seamless and efficient.
<br/><br/><br/><br/>

<img width="1842" height="882" alt="image" src="https://github.com/user-attachments/assets/f4d8dad8-d221-4619-8122-7c6c8e983928" /><br/>
Agent takes natural language queries from users and automatically converts them into OData $filter strings that match the SharePoint list schema. Once the filter query is built, it triggers the Get SharePoint Data tool (a Power Automate flow) to fetch matching rows directly from the library catalog. This allows users to search by author, publisher, genre, dates, or other attributes without knowing technical query syntax.

---

<img width="1846" height="876" alt="image" src="https://github.com/user-attachments/assets/d9384bad-27aa-4050-bfbd-2f37ea27604a" /><br/>
Agent passes the filter query to Power Automate, which handles both retrieving data from SharePoint and performing aggregate functions. Within the flow, operations like counting rows, finding the highest or lowest price, and extracting distinct values are executed before sending the final result back to the user. This ensures all logic runs inside Power Automate without needing extra processing in the agent.

---

Instructions for 'Get SharePoint Data' tool which is triggering Power Automate Flow:
```
Use this tool to fetch data from the library's SharePoint list based on user questions.
It triggers when users ask about books, authors, genres, publishers, or any catalog-related info.
The tool accepts a filter query that defines the search criteria (e.g., author name, release year, price range).
The expected input is a SharePoint-compliant OData $filter string.
It returns matching rows from the library book list without any aggregation or totals.
```


