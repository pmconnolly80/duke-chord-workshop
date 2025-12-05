I have analyzed the codebase and have generated a detailed README for the Duke Chord project.

```markdown
# Duke & Chord Project Documentation

This document provides a comprehensive overview of the Duke & Chord project, detailing its architecture, design patterns, and implementation strategies. It aims to be a valuable resource for new developers, enabling them to quickly understand and contribute to the project.

## Table of Contents

1.  [Project Overview](#1-project-overview)
2.  [Architecture](#2-architecture)
3.  [Data Management](#3-data-management)
4.  [Component Structure](#4-component-structure)
5.  [Backend (JSON-Server)](#5-backend-json-server)
6.  [Styling](#6-styling)
7.  [Getting Started](#7-getting-started)
    *   [Installation](#installation)
    *   [Running the Application](#running-the-application)
8.  [Key Files and Directories](#8-key-files-and-directories)

## 1. Project Overview

Duke & Chord is a web application designed for a distinctive musical experience. Users can find new and used instruments, and sign up for expert classes with professional musicians. The application provides a user authentication system, a catalog for instruments, a listing for classes, and a way to view details for each.

## 2. Architecture

The application follows a modular, component-based architecture with a clear separation of concerns. It is built using vanilla JavaScript for the frontend, leveraging event-driven patterns for state management and view rendering. The backend is powered by `json-server`, serving data from a static JSON file.

### Frontend Architecture

The frontend is structured around a central `main.js` file that orchestrates the initial rendering and listens for global state changes. Components are responsible for rendering specific parts of the UI and dispatching custom events when user interactions or data updates occur.

```mermaid
graph TD
    A[index.html] --> B(main.js);
    B -- Initial Render --> C(Header.js);
    B -- Authenticated --> D(DukeChord.js);
    B -- Not Authenticated --> E(Login.js/Register.js);
    D -- Renders --> F(NavBar.js);
    D -- Renders based on URL parameter --> G(View Components);
    G -- Examples --> H(Home.js);
    G -- Examples --> I(InstrumentList.js);
    G -- Examples --> J(ClassList.js);
    F -- Navigates --> G;
    E -- Authenticates --> D;
    SubGraph View Components
        H -- Renders --> K(MiniInstrument.js);
        H -- Renders --> L(MiniClass.js);
        I -- Renders --> M(Instrument.js);
        M -- Clicks to --> N(InstrumentDetail.js);
        J -- Renders --> O(Class.js);
        O -- Clicks to --> P(ClassDetails.js);
    End
    B -- "CustomEvent('stateChanged')" --> B;
```

### Backend Architecture

The backend is a simple `json-server` instance. It provides a RESTful API for fetching and manipulating data stored in `api/database.json`.

```mermaid
graph TD
    A[Frontend] --> B(HTTP Requests);
    B --> C[server.js (json-server)];
    C -- Reads/Writes --> D[api/database.json];
    D -- Supplies Data --> C;
    C -- Serves HTML/Static Assets --> A;
```

## 3. Data Management

The application employs a centralized state management pattern, where data is stored in dedicated "state manager" modules within `src/scripts/data/`.

*   **`StateManager` Modules:** Each major entity (e.g., Classes, Instruments, Users) has its own state manager (`ClassStateManager.js`, `InstrumentsStateManager.js`, `UserStateManager.js`). These modules are responsible for:
    *   Maintaining the current state of their respective data (e.g., `state.classes`, `state.instruments`).
    *   Providing functions to fetch data from the API (e.g., [`fetchAllClasses()`](src/scripts/data/ClassStateManager.js:33)).
    *   Providing getter functions to retrieve a copy of the current state (e.g., [`getClasses()`](src/scripts/data/ClassStateManager.js:43)).
    *   Dispatching a custom DOM event (`"stateChanged"`) on the main content container (`#content`) whenever the state is updated. This allows other parts of the application to react to data changes and re-render.
*   **`ViewStateManager.js`**: Handles URL parameter management for routing and dispatches `popstate` events to trigger view updates.
*   **`Settings.js`**: Stores application-wide settings, such as the API URL.

**Key Strategy: Event-Driven State Updates**

The core data management strategy involves:
1.  Fetching data from the API and updating the internal state in a state manager.
2.  Dispatching a custom `"stateChanged"` event.
3.  The `main.js` file listens for this event and re-renders the entire application, ensuring the UI reflects the latest state. This is a common pattern in single-page applications without a dedicated framework like React or Vue.

## 4. Component Structure

The frontend components are vanilla JavaScript functions that return HTML strings (using template literals). This is a form of "component-based rendering" without a full-fledged framework.

*   **HTML Generation:** Each component function (e.g., [`Home()`](src/scripts/Home.js:7), [`LoginForm()`](src/scripts/auth/Login.js:31)) is responsible for generating the HTML for its specific section of the UI.
*   **Event Listeners:** Components attach event listeners to the `#content` container to handle user interactions within their scope (e.g., clicks on navigation links, form submissions).
*   **Modularity:** Components are organized into logical directories (e.g., `auth`, `classes`, `instruments`, `nav`) to promote modularity and maintainability.
*   **Mini Components:** Smaller, reusable components like [`MiniInstrument()`](src/scripts/instruments/MiniInstrument.js:1) and [`MiniClass()`](src/scripts/classes/MiniClass.js:1) are used to render lists of items efficiently.

## 5. Backend (JSON-Server)

The backend is implemented using [`json-server`](https://github.com/typicode/json-server), a powerful tool for quickly setting up a fake REST API.

*   **`server.js`**: This file configures and starts the `json-server`. It sets up:
    *   The database file (`api/database.json`).
    *   Static file serving from the `src` directory.
    *   URL rewriting to simplify API routes (`/api/*` becomes `/$1`).
    *   A catch-all route to serve `index.html` for non-API requests, enabling client-side routing.
*   **`api/database.json`**: This file acts as the application's database, containing collections of data (e.g., `classes`, `musicians`, `instrumentTypes`, `instruments`, `users`). `json-server` automatically provides RESTful endpoints for each top-level key in this JSON file.

## 6. Styling

The application uses plain CSS for styling, with separate CSS files for different sections or components to promote organization.

*   **`src/styles/`**: This directory contains all the CSS files.
*   **Modular CSS:** Styles are organized by feature or component (e.g., `class.css`, `instruments.css`, `nav.css`).
*   **`main.css`**: Likely the main entry point for styles, importing or linking other CSS files.

## 7. Getting Started

Follow these instructions to get the Duke & Chord project up and running on your local machine.

### Installation

1.  **Clone the repository:**
    ```bash
    git clone <repository_url>
    cd duke-chord-workshop
    ```
2.  **Install dependencies:**
    ```bash
    npm install
    ```

### Running the Application

1.  **Start the JSON server:**
    ```bash
    npm start
    ```
    This will start the `json-server` on `http://localhost:5002` (or the port specified in `server.js`).
2.  **Open in browser:**
    Navigate to `http://localhost:5002` in your web browser.

## 8. Key Files and Directories

*   **`/`**:
    *   [`package.json`](package.json): Project metadata and dependencies.
    *   [`server.js`](server.js): Configures and runs the `json-server` backend.
    *   [`README.md`](README.md): This document.
*   **`api/`**:
    *   [`database.json`](api/database.json): The mock database for `json-server`.
*   **`src/`**:
    *   [`index.html`](src/index.html): The main HTML file for the application.
    *   **`src/audio/`**: Contains audio files for instrument sounds.
    *   **`src/images/`**: Contains images used throughout the application.
    *   **`src/scripts/`**: All frontend JavaScript logic.
        *   [`main.js`](src/scripts/main.js): Application entry point, handles routing and state change listeners.
        *   [`DukeChord.js`](src/scripts/DukeChord.js): Main application component, renders navigation and current view.
        *   [`Home.js`](src/scripts/Home.js): Home view component.
        *   **`src/scripts/auth/`**: Authentication-related components (Login, Register).
        *   **`src/scripts/classes/`**: Components for displaying and managing classes.
        *   **`src/scripts/data/`**: State management modules.
            *   [`ClassStateManager.js`](src/scripts/data/ClassStateManager.js): Manages class data.
            *   [`InstrumentsStateManager.js`](src/scripts/data/InstrumentsStateManager.js): Manages instrument data.
            *   [`UserStateManager.js`](src/scripts/data/UserStateManager.js): Manages user authentication and data.
            *   [`ViewStateManager.js`](src/scripts/data/ViewStateManager.js): Handles URL and view state changes.
            *   [`Settings.js`](src/scripts/data/Settings.js): Application settings.
        *   **`src/scripts/employees/`**: Components related to employee listings ("About Us").
        *   **`src/scripts/instruments/`**: Components for displaying and managing instruments.
        *   **`src/scripts/nav/`**: Navigation components (Header, NavBar).
    *   **`src/styles/`**: All CSS files for styling the application.
```