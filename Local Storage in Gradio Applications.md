# Local Storage in Gradio Applications

This repository provides a comprehensive guide on integrating the **Gemini API** into applications and implementing **local storage** for conversation history in **Gradio**-based web applications. Below, you'll find an overview of the limitations of the Gemini API, best practices for handling conversation history, and technical details on implementing local storage using `localStorage` and `IndexedDB`.

---

## Table of Contents
1. [Limitations of the Gemini API](#limitations-of-the-gemini-api)
   - [Stateless Nature and Lack of Persistent Conversation History](#stateless-nature-and-lack-of-persistent-conversation-history)
   - [Additional Notable Limitations](#additional-notable-limitations)
2. [Workarounds and Best Practices](#workarounds-and-best-practices)
   - [Handling Conversation History](#handling-conversation-history)
   - [Efficient Context Management](#efficient-context-management)
3. [Local Storage in Gradio Applications](#local-storage-in-gradio-applications)
   - [Introduction](#introduction)
   - [Integration of Custom JavaScript in Gradio](#integration-of-custom-javascript-in-gradio)
   - [Implementing Conversation History with `localStorage`](#implementing-conversation-history-with-localstorage)
   - [Implementing Conversation History with `IndexedDB`](#implementing-conversation-history-with-indexeddb)
   - [Comparison of `localStorage` and `IndexedDB`](#comparison-of-localstorage-and-indexeddb)
4. [Conclusion](#conclusion)

---

## Limitations of the Gemini API

### Stateless Nature and Lack of Persistent Conversation History
- **No Native Context Persistence:** The Gemini API processes each request in isolation and does not store previous interactions unless explicitly provided with the conversation history.
- **In-Memory ChatSession:** The SDK's `ChatSession` object manages conversation history only during an active session. However, this data is stored in RAM—restarting or crashing the process results in data loss.

### Additional Notable Limitations
- **Context Window Management:** While models like Gemini 1.5 Pro support large context windows (up to 2 million tokens), efficient token usage is crucial, especially in long conversations.
- **Cost Considerations:** Repeatedly sending extensive conversation histories or large context inputs can increase processing costs. Strategies like **Context Caching** are recommended to mitigate this.

---

## Workarounds and Best Practices

### Handling Conversation History
- **Explicitly Transmit Context:** Always include the relevant conversation history in each request payload, formatted as an array of messages with roles (e.g., "user", "model").
- **Persist History Externally:** Store conversation logs in an external database or storage service. Options include:
  - **NoSQL databases** (Firestore, MongoDB)
  - **Relational databases** (PostgreSQL, MySQL)
  - **Local storage** (`localStorage` or `IndexedDB` for client-side apps)
  - **Cloud storage** (Google Cloud Storage, AWS S3 for JSON logs)
- **Leverage In-Memory Sessions:** Use the Gemini SDK's `ChatSession` object for short-term history management and persist the session externally when it ends.

### Efficient Context Management
- **Monitor Token Usage:** Regularly check conversation history length and implement strategies like:
  - **Truncating or Summarizing Older Messages:** Keep only the most relevant context to stay within token limits.
  - **Context Caching:** Reuse initial context to reduce redundancy and costs.

---

## Local Storage in Gradio Applications

### Introduction
Persisting conversation history enhances user experience in dialog-based web applications. While server-side storage is robust, client-side options like `localStorage` and `IndexedDB` offer simpler alternatives, especially for smaller applications or those with privacy concerns.

### Integration of Custom JavaScript in Gradio
Gradio, a Python-based framework for building ML web applications, requires custom JavaScript to interact with browser APIs like `localStorage` and `IndexedDB`. There are three main methods to add JavaScript to a Gradio application:
1. **Using the `js` Parameter in `gr.Blocks()` or `gr.Interface()`:** Executes JavaScript once when the application loads.
2. **Using the `js` Argument in Event Listeners:** Links JavaScript code to specific events triggered by Gradio components.
3. **Using the `head` Parameter in `gr.Blocks()`:** Adds HTML elements, including `<script>` tags, to the application's `<head>`.

### Implementing Conversation History with `localStorage`
- **Basics:** `localStorage` is a synchronous key-value storage mechanism in the browser, with a typical capacity of 5-10 MB per domain.
- **Storing and Retrieving JSON:** Serialize conversation history with `JSON.stringify()` before storing and deserialize with `JSON.parse()` when retrieving.
- **Saving on New Message:** Use event listeners to trigger JavaScript functions that update and save the conversation history.
- **Loading on Application Start:** Retrieve and parse the stored conversation history when the application loads.

### Implementing Conversation History with `IndexedDB`
- **Introduction:** `IndexedDB` is an asynchronous, transactional client-side database system with greater storage capacity and support for complex data structures.
- **Setting Up the Database:** Open or create a database and define object stores and indexes in the `onupgradeneeded` event.
- **Asynchronous Data Storage and Retrieval:** Use transactions to store and retrieve data asynchronously, ensuring non-blocking operations.
- **Event Handling:** Use Gradio events to trigger `IndexedDB` operations for updating and saving conversation history.

### Comparison of `localStorage` and `IndexedDB`
| Feature                | `localStorage`                          | `IndexedDB`                              |
|------------------------|-----------------------------------------|------------------------------------------|
| Storage Capacity       | Limited (5-10 MB)                       | Larger (up to gigabytes)                 |
| API Complexity         | Simple                                  | Complex                                  |
| Synchronization        | Synchronous (blocking)                  | Asynchronous (non-blocking)              |
| Data Structure Support | Strings only (requires JSON serialization) | Supports objects, arrays, binary data    |
| Indexing               | No built-in indexing                    | Supports indexing for efficient queries  |
| Transactions           | No transaction support                  | Supports transactions for data integrity |
| Web Workers            | Cannot be used in Web Workers           | Can be used in Web Workers               |
| Error Handling         | Typically `try-catch`                   | Uses event handlers (`onsuccess`, `onerror`) |

---

## Conclusion
Implementing local storage for conversation history in Gradio applications significantly enhances user experience. Both `localStorage` and `IndexedDB` offer client-side storage options, each with its own advantages and trade-offs. The choice between them depends on the application's specific requirements and expected usage. For simpler applications with limited conversation history, `localStorage` may suffice, while `IndexedDB` is better suited for more complex scenarios with larger data volumes and the need for asynchronous operations.

For further details, consult the [Gradio documentation](https://gradio.app/docs/) and [MDN Web Docs](https://developer.mozilla.org/) for `localStorage` and `IndexedDB`.

--- 

Feel free to contribute or raise issues for improvements!
