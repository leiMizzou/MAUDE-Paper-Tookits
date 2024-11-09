# Paper Info Completion

A Python-based tool for fetching abstracts and full-text links of PubMed articles, enhancing your research workflow by automating data retrieval and management.

## Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Configuration](#configuration)
- [Usage](#usage)
  - [Step 1: Fetch Abstracts and Full-Text Links](#step-1-fetch-abstracts-and-full-text-links)
  - [Step 2: Update Missing Abstracts](#step-2-update-missing-abstracts)
  - [Notes on Using Scholarly with VPNs](#notes-on-using-scholarly-with-vpns)
- [Input and Output Files](#input-and-output-files)
- [Database](#database)
- [Error Handling](#error-handling)
- [Rate Limiting](#rate-limiting)
- [License](#license)
- [Acknowledgements](#acknowledgements)

## Overview

The **PubMed Articles Processor** automates the retrieval of abstracts and full-text links for a list of PubMed articles. It reads an input CSV file containing PubMed IDs (PMIDs), article titles, and publication dates, fetches the corresponding abstracts and full-text links using NCBI's E-utilities API, and outputs the enriched data to a new CSV file. Additionally, it provides functionality to update records with missing abstracts by searching via article titles using the `scholarly` library.

## Features

- **Fetch Abstracts by PMID**: Retrieves abstracts for articles using their PMIDs.
- **Search Abstracts by Title**: Attempts to find abstracts by searching article titles when PMIDs do not yield results.
- **Retrieve Full-Text Links**: Obtains links to the full text of articles available on PubMed Central (PMC).
- **Data Persistence**: Uses TinyDB to store fetched article data.
- **Error Handling**: Gracefully handles errors and logs issues encountered during data retrieval.
- **Rate Limiting**: Implements delays between API requests to comply with NCBI's usage policies.
- **Backup Mechanism**: Creates backups of output CSV files before updating missing abstracts.
- **VPN Recommendations**: Advises on using specific VPN settings to improve results when fetching missing abstracts.

## Prerequisites

- Python 3.7 or higher
- [NCBI E-utilities API](https://www.ncbi.nlm.nih.gov/books/NBK25499/) access
- An active internet connection

## Installation

1. **Clone the Repository**

   ```bash
   git clone https://github.com/yourusername/pubmed-articles-processor.git
   cd pubmed-articles-processor
   ```

2. **Create a Virtual Environment (Optional but Recommended)**

   ```bash
   python3 -m venv venv
   source venv/bin/activate  # On Windows: venv\Scripts\activate
   ```

3. **Install Dependencies**

   ```bash
   pip install -r requirements.txt
   ```

   *If `requirements.txt` is not provided, install the necessary packages manually:*

   ```bash
   pip install requests tinydb pandas scholarly
   ```

## Configuration

Before running the scripts, configure your email and (optionally) your NCBI API key in the script:

1. **Open the Script**

   Open the Python script in your preferred text editor.

2. **Set Your Email**

   ```python
   EMAIL = "your_email@example.com"  # Replace with your valid email address
   ```

   NCBI requires an email address for API usage.

3. **Set Your NCBI API Key (Optional)**

   If you have an NCBI API key, add it here to increase your rate limits:

   ```python
   API_KEY = "your_ncbi_api_key"  # Leave empty if you don't have one
   ```

   *To obtain an API key, sign up at [NCBI](https://www.ncbi.nlm.nih.gov/account/settings/).*

## Usage

The project consists of two main scripts:

1. **Fetching Abstracts and Full-Text Links**
2. **Updating Missing Abstracts**

### Step 1: Fetch Abstracts and Full-Text Links

This script reads an input CSV file, fetches abstracts and full-text links for each article, and writes the results to an output CSV file.

1. **Prepare the Input CSV**

   Ensure your input CSV (`maude_papers.csv`) contains the following columns:

   - `PMID`: PubMed ID of the article
   - `Title`: Title of the article
   - `Publication Date`: Publication date of the article

2. **Run the Script**

   ```bash
   python fetch_abstracts.py
   ```

   *Replace `fetch_abstracts.py` with the actual script filename.*

   The script will generate `maude_papers_output.csv` containing the original data along with two new columns:

   - `Abstract`: The fetched abstract or an error message
   - `FullTextLink`: Link to the full text on PMC or an error message

### Step 2: Update Missing Abstracts

This script scans the output CSV for records with missing abstracts and attempts to fetch them using the `scholarly` library.

1. **Run the Update Script**

   ```bash
   python update_missing_abstracts.py
   ```

   *Replace `update_missing_abstracts.py` with the actual script filename.*

   The script will:

   - Create a backup of the original output CSV (`maude_papers_output_backup.csv`)
   - Search for missing abstracts by article title
   - Update the output CSV with any found abstracts

### Notes on Using Scholarly with VPNs

When using the `scholarly` library to fetch missing abstracts, the results may vary depending on the VPN used. If you encounter issues where abstracts cannot be found:

- **Change Your VPN**: Try connecting through different VPN servers.
- **Recommended VPN Location**: It is recommended to use a US-based VPN to improve the chances of successfully retrieving abstracts.
- **Retry Multiple Times**: Sometimes, multiple attempts with different VPN settings may yield better results.

This is due to potential geo-restrictions or rate limiting implemented by Google Scholar, which `scholarly` relies on.

## Input and Output Files

- **Input CSV (`maude_papers.csv`)**

  | PMID      | Title                          | Publication Date |
  |-----------|--------------------------------|-------------------|
  | 12345678  | Example Article Title          | 2023-01-01        |
  | ...       | ...                            | ...               |

- **Output CSV (`maude_papers_output.csv`)**

  | PMID      | Title                          | Publication Date | Abstract                     | FullTextLink                                     |
  |-----------|--------------------------------|-------------------|------------------------------|--------------------------------------------------|
  | 12345678  | Example Article Title          | 2023-01-01        | This study explores...        | https://www.ncbi.nlm.nih.gov/pmc/articles/PMC12345678/ |
  | ...       | ...                            | ...               | ...                          | ...                                              |

## Database

The tool uses [TinyDB](https://tinydb.readthedocs.io/en/latest/) to store article data in a JSON file (`articles.json`). This allows for easy querying and data management.

## Error Handling

The scripts include comprehensive error handling to manage issues such as:

- Missing or malformed input files
- Network errors during API requests
- Missing expected fields in the CSV
- Errors during data parsing

Errors are logged to the console with descriptive messages to aid in troubleshooting.

## Rate Limiting

To comply with NCBI's usage policies and avoid triggering rate limits, the scripts include delays between API requests:

- **Fetching Data**: Pauses for approximately 0.34 seconds between requests (about 3 requests per second).
- **Updating Abstracts**: Pauses for 2 seconds between search queries to avoid triggering anti-scraping mechanisms.

## License

This project is licensed under the [MIT License](LICENSE).

## Acknowledgements

- [NCBI E-utilities](https://www.ncbi.nlm.nih.gov/books/NBK25499/) for providing access to PubMed data.
- [TinyDB](https://tinydb.readthedocs.io/en/latest/) for lightweight data storage.
- [Pandas](https://pandas.pydata.org/) for data manipulation.
- [Scholarly](https://pypi.org/project/scholarly/) for accessing Google Scholar data.

---

*Feel free to contribute to this project by submitting issues or pull requests.*

# License

MIT License

# Acknowledgements

- [NCBI E-utilities](https://www.ncbi.nlm.nih.gov/books/NBK25499/) for providing access to PubMed data.
- [TinyDB](https://tinydb.readthedocs.io/en/latest/) for lightweight data storage.
- [Pandas](https://pandas.pydata.org/) for data manipulation.
- [Scholarly](https://pypi.org/project/scholarly/) for accessing Google Scholar data.
