# go2web: Lightweight Web Request CLI Tool

## Introduction

go2web is a Python-based command-line interface (CLI) tool designed to simplify web interactions directly from the terminal. It offers capabilities for making HTTP/HTTPS requests and performing web searches without the need for external dependencies. This tool demonstrates fundamental networking concepts in Python, including socket programming, HTTP protocol handling, and basic content processing.

Developed as a lightweight alternative to more complex tools like curl or wget, go2web focuses on simplicity while still providing useful features like redirect following, content formatting, and search functionality.

## Features

- **Direct HTTP/HTTPS Requests**: Make GET requests to any website
- **Content Processing**:
  - HTML tag removal for cleaner text output
  - JSON formatting for improved readability
  - Basic content type detection
- **Redirect Handling**: Automatically follow HTTP redirects (up to 5 levels deep)
- **Response Caching**: Basic in-memory caching to avoid redundant requests
- **Web Search**: Integration with DuckDuckGo's API for search functionality
- **No External Dependencies**: Uses only Python standard libraries

## Code Explanation

### Core Components

1. **URL Handling**:
   - `parse_url()` uses `urlparse` to properly format and parse URLs, adding the HTTP scheme if missing
   - Extracts host, path, and query components needed for the request

2. **HTTP Request Construction**:
   - `create_http_request()` builds a standards-compliant HTTP GET request with appropriate headers
   - Includes User-Agent and Accept headers to identify the client and specify content preferences

3. **Socket Communication**:
   - `make_http_request()` establishes socket connections for both HTTP and HTTPS
   - Uses SSL context for secure connections
   - Handles receiving data in chunks until the response is complete

4. **Response Processing**:
   - `parse_http_response()` separates headers and body content
   - `remove_html_tags()` uses regular expressions to strip HTML markup
   - Special handling for JSON content with pretty printing

5. **Redirect Handling**:
   - Detects 301, 302, 303, and 307 status codes
   - Extracts the Location header
   - Recursively calls itself with a decremented redirect counter

6. **Search Functionality**:
   - `search_term()` connects to DuckDuckGo's API
   - `format_related_topics()` extracts and formats the search results

7. **Command Line Interface**:
   - Uses `argparse` to create a user-friendly interface
   - Implements mutually exclusive groups for URL requests and searches

### Key Technical Aspects

#### Socket-Level HTTP Communication

Rather than using higher-level libraries like `requests`, the code implements HTTP communication at the socket level:

```python
sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
if parsed_url.scheme == 'https':
    context = ssl.create_default_context()
    sock = context.wrap_socket(sock, server_hostname=host)
sock.connect((host, port))
```

This approach provides a deeper understanding of the HTTP protocol while demonstrating Python's networking capabilities.

#### Content Processing

The tool implements basic content processing to make responses more readable:

```python
def remove_html_tags(text):
    text = re.sub(r'<style[^>]*>[\s\S]*?</style>', '', text)
    text = re.sub(r'<script[^>]*>[\s\S]*?</script>', '', text)
    text = re.sub(r'<[^>]+>', '', text)
    text = re.sub(r'\s+', ' ', text)
    text = text.strip()
    return text
```

For JSON responses, it attempts to parse and pretty-print the content:

```python
if 'application/json' in content_type:
    try:
        parsed_json = json.loads(body)
        return json.dumps(parsed_json, indent=2)
    except:
        return body
```

## Installation

1. Clone this repository or download the go2web.py file
2. Ensure you have Python 3.6+ installed
3. No additional dependencies are required

## Usage

### Making HTTP Requests

```bash
python go2web.py -u <url>
```

Example:
```bash
python go2web.py -u example.com
```

### Searching the Web

```bash
python go2web.py -s "<search term>"
```

Example:
```bash
python go2web.py -s "python socket programming"
```

## Testing in PyCharm

### Setting Up Your Test Environment

1. **Import the Project**:
   - Open PyCharm
   - Select "Open" from the welcome screen
   - Navigate to the directory containing go2web.py
   - Click "OK"

2. **Configure Run Configurations**:
   - Click "Add Configuration" in the top-right corner
   - Click the "+" icon and select "Python"
   - Name: "go2web URL Test"
   - Script path: Select your go2web.py file
   - Parameters: `-u example.com`
   - Click "Apply"

3. **Create Additional Test Configurations**:
   - Repeat the above process to create different test scenarios:
     - URL with HTTPS: Parameters: `-u https://github.com`
     - Search test: Parameters: `-s "web development"`
     - JSON API test: Parameters: `-u https://jsonplaceholder.typicode.com/posts/1`

### Running Tests

1. **Basic URL Test**:
   - Select the "go2web URL Test" configuration
   - Click the green "Run" button or press Shift+F10
   - Verify that content from example.com appears in the Run window

2. **HTTPS Test**:
   - Select the HTTPS configuration
   - Run and verify that the secure connection works correctly

3. **JSON API Test**:
   - Select the JSON API configuration
   - Verify that the response is properly formatted as JSON

4. **Search Test**:
   - Select the Search configuration
   - Check that search results are displayed properly

### Debugging in PyCharm

1. **Set Breakpoints**:
   - Click in the gutter (left margin) next to lines you want to examine
   - Good places for breakpoints:
     - Line 125 (inside `make_http_request()`) to inspect the socket connection
     - Line 144 to see the response before processing
     - Line 43 to examine JSON parsing

2. **Start Debugging**:
   - Select a configuration
   - Click the green "Debug" button (or press Shift+F9)
   - Use the debug controls to step through execution:
     - Step Over (F8): Execute the current line and move to the next
     - Step Into (F7): Enter into a function call
     - Step Out (Shift+F8): Complete the current function and return
   - Examine variables in the "Variables" panel

3. **Watch Expressions**:
   - Add expressions to watch in the "Watches" panel
   - Right-click and select "New Watch" to add variables you want to monitor

## Potential Improvements

- Implement POST, PUT, and DELETE methods
- Add support for custom headers and cookies
- Implement more robust error handling
- Add timeout options for requests
- Expand the search functionality with more providers
- Implement file download capabilities
- Add proxy support
- Create a more comprehensive caching mechanism

## Conclusion

go2web demonstrates fundamental concepts of web communication in Python using only standard libraries. While not intended to replace full-featured tools like curl, wget, or Python's requests library, it serves as an educational example of low-level socket programming for HTTP requests.

The tool's simplicity makes it ideal for learning purposes while still providing practical functionality for basic web interactions and searches. Its modular design allows for easy expansion and customization, making it a solid foundation for more complex networking applications.

By examining and testing this code in PyCharm, you can gain insights into Python socket programming, HTTP protocol handling, content processing, and command-line application development.
