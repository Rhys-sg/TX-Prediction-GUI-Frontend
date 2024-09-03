# Interface Component README

This component is the main GUI, containing the other templates and controlling information between them.

## Component Structure

### Template

- **AppHeader**
  - Displays user information and provides actions for viewing student ligations, new input, user login, and user account.
  
- **Center Interface**
  - **Top Left Cell**
    - Displays a card with an image and a label "GFP".
  
  - **Top Center Cell**
    - Shows the upper and lower coding strands of the sequence along with -10 and -35 labels.
  
  - **Top Right Cell**
    - Displays a card with an image and a label "RFP".
  
  - **Bottom Left Cell**
    - Contains buttons for "Cut", "Default", "Predict", and "Ligate", each with interactive states.
  
  - **Bottom Center Cell**
    - Includes an image and a `TextInput` component for updating the promoter sequence.
  
  - **Bottom Right Cell**
    - Displays a `BarChart` component for predicted and observed transcription values.

### Dialogs

- **Ligate Popup**
  - Allows users to input data for ligation and submit it to the database.
  
- **Student Ligation Database**
  - Provides a view of student ligation orders.
  
- **New Input Popup**
  - Allows users to enter new input data.
  
- **User Login**
  - Facilitates user login with credentials.
  
- **User Account**
  - Manages user account settings and logout.

### Script

- **Data**
  - Contains state variables for handling user interactions, sequence data, predictions, and dialog visibility.
  
- **Methods**
  - Includes functions for cutting and updating sequences, predicting values, handling user input, and managing dialog states.
  
- **Lifecycle Hooks**
  - `created()` initializes sequence data and visual elements.

### Style

- **Scoped Styles**
  - Defines the visual appearance of various elements including background colors, text styles, and positioning for sequence labels.

## Usage

1. **Import the Component:**
   Import the component into your Vue.js application and use it within your templates.

   ```javascript
   import Interface from './Interface.vue';
   ```

2. **Props:**
   - `sectionName` (String): A prop to pass additional section-specific information.

3. **Interaction:**
   - Interface contains the buttons used to cut, insert default, predict, and ligate.

4. **Backend Integration:**
   - The component interacts with a backend server via `axios` to fetch predictions and observed values.

5. **Dialogs:**
   - Manage dialog visibility through the component's state. Each dialog handles specific tasks related to user interaction and data entry.

## Dependencies

- **Vuetify:** For layout and styling.
- **Axios:** For HTTP requests to the backend.

## Installation

For local hosting, ensure you have Vuetify and Axios installed in your Vue.js project. You can install them using npm:

```bash
npm install vuetify axios
```