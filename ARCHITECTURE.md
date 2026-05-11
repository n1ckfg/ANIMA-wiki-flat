# Architecture

This project is a static, single-page application (SPA) that visualizes a knowledge graph in the browser. It is designed to be fully self-contained and run locally using a simple HTTP server without requiring a backend API or database.

## System Components

### 1. Main Application (`index.html`)

The entire application logic, structure, and dataset are bundled into a single HTML file, acting as a standalone client.

*   **Data Store (Inline JSON):** The knowledge graph data is embedded directly within `<script>` tags as large JavaScript arrays (`originalNodes` and `originalEdges`). This approach avoids asynchronous fetching and CORS issues, ensuring immediate availability when the page loads. The data contains rich metadata, including pre-rendered markdown and structural relationships.
*   **User Interface (HTML/CSS):** The layout consists of a full-screen canvas for the graph, a floating control panel for search and filtering, and an off-canvas drawer for displaying detailed node information. Styling is handled via inline CSS, implementing a dark theme with glassmorphism effects. The legend colors for node types (source: `#4CAF50` green, entity: `#2196F3` blue, concept: `#FF9800` orange, synthesis: `#9C27B0` purple) are stored as inline styles on `<span>` elements within the `#controls` block in `index.html`.
*   **Application Logic (JavaScript):**
    *   **Graph Orchestration:** Manages the lifecycle of the `vis-network` instance, feeding it filtered views of the nodes and edges.
    *   **State Management:** Handles user interactions like node selection, searching, and filtering. It dynamically computes visual emphasis (colors, opacities, sizes) based on the active state and network topology (adjacency lists).
    *   **Markdown Rendering:** Includes a custom, lightweight markdown parser (`renderMarkdown`) to convert the raw markdown text stored in the node data into HTML for display in the side drawer.

### 2. Graph Visualization Engine (`vis-network.min.js`)

A bundled third-party library used for the core rendering of the network graph. It handles:
*   Drawing nodes and edges on an HTML5 Canvas.
*   Physics simulations (Barnes-Hut) to auto-layout the graph and handle forces like gravity and spring lengths.
*   Low-level interactions like zooming, panning, and hovering.

### 3. Typography (`fonts/`)

The application uses a locally hosted font (`Inter`) to ensure consistent rendering across different environments without relying on external CDNs.

### 4. Launcher Scripts (`run.command`, `run.bat`)

Convenience scripts provided for macOS/Linux and Windows environments. These scripts:
*   Automatically find an available local port starting at 8080.
*   Launch a local static file server (expecting `http-server` to be available in the environment).
*   Open the default web browser to the correct local URL.

## Data Flow

1.  **Initialization:** The browser loads `index.html`, which immediately parses the inline `originalNodes` and `originalEdges` arrays.
2.  **Rendering:** The `applyFilters()` function builds the initial state, passing the visible nodes and edges to the `vis.Network` instance.
3.  **Interaction:** 
    *   When a user searches or changes filter sliders, `applyFilters()` is called to re-evaluate the visibility and styling of all nodes and edges, updating the `vis-network` instance dynamically.
    *   When a user clicks a node, the application calculates its immediate neighbors using an adjacency map, highlights the subgraph, and populates the side drawer with the node's rich markdown content.