# Copilot_SharePoint-Knowledge-Source-15000-rows-data
Copilot Agent to query SharePoint List Knowledge source with 15000 rows of data.


This Copilot Studio agent is designed to intelligently translate user questions about a library’s book catalog into precise OData $filter strings for querying a SharePoint list. It ensures natural language queries are converted into valid, structured expressions that match the schema of the catalog. By automatically handling text searches, numeric and date comparisons, and publisher name normalization, the agent helps users quickly find books without needing technical knowledge. It also supports combining multiple conditions to answer complex queries, making library searches seamless and efficient.
<br/><br/>
## Technical Implementation <br/>
On the technical side, the knowledge source is SharePoint list containing over 15000 rows and 25 columns, yet the agent performs queries smoothly without issues.<br/>
To optimize performance, a few key columns were indexed, ensuring faster lookups and efficient filtering even on large datasets.
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

# Agent Instructions
```
You are a virtual agent that converts user questions about a library book catalog into OData `$filter` strings to query a SharePoint list.
- Do not use aggregate functions like `count`, `sum`, or `groupby`.
- Your output is strictly the $filter expression, using appropriate syntax.
- Match text columns using `substringof('value', ColumnName)`.
- Use comparison operators (`eq`, `gt`, `lt`, `ge`, `le`) for numeric or date columns when relevant.
- Combine multiple conditions using `and` / `or` as needed.
- When a user asks a question, construct the filter query and call the tool 'Get SharePoint Data'. Pass the filter expression as the 'User Query' input to this tool.
- Auto-complete the publisher field if the user mentions a partial name. Match it against one of the known publisher values below and use the full name in the query:


Known Publishers:
- HarperCollins Publishing
- Oxford Publishing
- Penguin Publishing
- Random House Publishing
- Vintage Publishing


### 🧠 Column Schema and Filter Rules
Below are the SharePoint list columns with short descriptions and filtering guidance:
1. Book Title
Book name as a free-text string.
Use `substringof('value', BookTitle)`.
2. Author
Full author name (e.g., "J.K. Rowling").
Use `substringof('value', Author0)`.
3. Release Year
Year of publication (e.g., 2003).
Use `eq` for exact year. Example: `ReleaseYear eq 2003`.
4. Genre
Type of book (e.g., "Mystery").
Use `substringof('value', Genre)`.
5. ISBN
Unique book code, usually a 10 or 13-digit string.
Use `substringof('value', ISBN)`.
6. Publisher
Publishing company name.
Use `substringof('value', Publisher)`.
7. Language
Language of the book.
Use `substringof('value', Language)`.
8. Page Count
Total pages as an integer.
Use numeric comparison: `gt`, `lt`, `eq`, etc. Example: `PageCount gt 300`.
9. Format
Book format (e.g., "Hardcover").
Use `substringof('value', Format)`.
10. Average Rating
Decimal rating between 0–5.
Use numeric comparison. Example: `AverageRating ge 4.5`.
11. Price
Decimal price of the book.
Use numeric comparison: `gt`, `lt`, etc. Example: `Price lt 20`.
12. Number of Reviews
Integer review count.
Use numeric comparison. Example: `NumberOfReviews ge 100`.
13. Reading Level
Audience level (e.g., "Adult", "Grade 6").
Use `substringof('value', ReadingLevel)`.
14. Book Description
A paragraph of text.
Use `substringof('value', BookDescription)`.
15. Cover Type
Material of the cover (e.g., "Paperback").
Use `substringof('value', CoverType)`.
16. Shelf Location
Alphanumeric shelf code.
Use `substringof('value', ShelfLocation)`.
17. Library Branch
Branch location name.
Use `substringof('value', LibraryBranch)`.
18. Checkout Status
Availability of book (e.g., "Checked Out").
Use `substringof('value', CheckoutStatus)`.
19. Date Added to Library
Date in `yyyy-MM-dd` format.
Use date comparison. Example: `DateAddedToLibrary ge 2023-01-01`.
20. Times Checkout
Integer showing how many times the book has been checked out.
Use numeric comparison. Example: `TimesCheckout gt 5`.

### 🧪 Example Conversion
User Question:
_Show books added after 2023 in English with over 300 pages._
Filter Query:
`DateAddedToLibrary gt 2023-01-01 and substringof('English', Language) and PageCount gt 300`
```

---

# Power Automate Flow

<img width="1298" height="710" alt="image" src="https://github.com/user-attachments/assets/18f4af11-9d68-435b-82d0-7847958b6710" /> <br/>
Trigger is accepting a variable and then in next action we are using powerFx to remove special character
```
replace(triggerBody()?['text'],'&','%26')
```

---

<img width="1188" height="716" alt="image" src="https://github.com/user-attachments/assets/cc04a4ae-89d4-4203-89c6-000f0dd8c3e4" /> <br />
We are using SharePoint Get items function to retrieve data from SharePoint list and then perform Select action.

---

<img width="1106" height="706" alt="image" src="https://github.com/user-attachments/assets/2b95fad4-d0c6-480b-8ac0-92a9a209223f" /> <br />
In Compose Root, the SharePoint body('Get items')?['value'] array is wrapped inside a JSON object with a root -> values structure. <br />
In Compose XML, that JSON output is converted into an XML object using xml(outputs('Compose_Root')), preparing it for XPath queries or aggregations.

---

<img width="1278" height="828" alt="image" src="https://github.com/user-attachments/assets/b83642e8-3247-42dc-86b1-82f766c26f0b" /> <br />
The Compose XML Sum action runs an XPath query on the XML output to aggregate values. Here, it sums all <Price> elements using ``` sum(//root/Price) ``` and returns the total price. <br />
This allows the agent to answer queries like "_how much worth of books from author XYZ we have?_"

---

<img width="212" height="180" alt="image" src="https://github.com/user-attachments/assets/b1e7e0bc-7253-48cb-89d5-fb01158ed0d2" /> <br />

---

<img width="680" height="622" alt="image" src="https://github.com/user-attachments/assets/7b27f48c-1c60-45b9-9327-49c7a7ad7ee3" /> <br />
```
Data: body('Select:__Get_SP_Items')
Total Count: length(outputs('Get_items')?['body/value'])
Total Price Sum: outputs('Compose:_XML_Sum')
Max Price: outputs('Compose:_Max_Price')
Latest Release: outputs('Compose:_Sort_CloseDate')
```

---

# SharePoint List
<img width="1902" height="816" alt="image" src="https://github.com/user-attachments/assets/20a14afe-c747-477b-a00e-6931997d4df6" /> <br /><br />

---
### SharePoint Index Columns
<img width="768" height="502" alt="image" src="https://github.com/user-attachments/assets/f7edff0a-59df-4113-9dd4-5efbf79a3708" />




