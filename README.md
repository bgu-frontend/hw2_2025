# Introduction
We will continue from hw1, the previous website. You will write a backend express server and use a mongo database instead of the JSON server.

## Submission
- Coding: 70%, Questions: 30%.
- Your submitted git repo should be *private*, please add 'barashd@post.bgu.ac.il', 'Gurevichronen@gmail.com', 'daninost', and 'Gal-Fadlon' to the list of collaborators.
- Do not use external libraries that provide the pagination component. If in doubt, contact the course staff.
- Deadline: 18.5.25, end of day.
- Additionally, solve the [theoretical questions](https://forms.gle/R1xSihBMavb2tWzC6).
- Use TypeScript, and follow the linter's warnings (see eslint below). The linter can be faulty; use it to get early signs of bugs, the automatic tests will not take away points for linter warnings.
- The ex2 forum is open for questions in Moodle. It's highly recommended to use it for design, technical issues, and any general subject.

- Git repository content:
  - Aim for a minimal repository size that can be cloned and installed: most of the files in github should be code and package dependencies (add package.json, index.html).
  - Don't submit (add to git) .env (it's your secret file which includes passwords- add to gitignore), node_modules dir, package-lock.json, or note json files.
  - the submission commit will be tagged as "submission_hw2" using the same github link as hw1. (See [git tagging](https://git-scm.com/book/en/v2/Git-Basics-Tagging))
  - to test your submission, run the presubmission script (in github). A submission that does not pass the presubmission script, gets a 0 score automatically.
      For example: 
      ```bash
      bash presubmission.sh git@github.com:bgu-frontend/hw1_2025.git ./.env
      ``` 
      - Tip: You can use a local git directory as the target git repository.
      - Tip: You can make the presubmission script to run automatically during every git push.
      - Tip: `-x` argument to bash shows the currently executed line.

## AI
Tip for using an AI Assistant: You can ask questions and read the answers, but avoid copying them. Understand the details but write the code yourself.

Requirements for AI usage:
- If you use an AI assistant (e.g., ChatGPT, Claude, etc.), you must share your conversation with the AI:
    - Use the "share" option provided by the AI tool to generate a link to the conversation.
    - If the tool does not have a "share" option, provide a PDF containing screenshots of the chat.
- Submit the shared conversation link or PDF along with your homework.
- You are expected to fully understand your own code, and be able to answer questions about it, frontally.
## Plagiarism
- We use a plagiarism detector.
- The person who copies and the person who was copied from are both responsible. Set your repository private, and don't share your code.
- If the code was copied from an AI agent, we expect it to appear in the shared conversation above.
- Exception: You can, and it's recommended, to share tests with other students, and share ideas and thoughts in the forum.

## Tools and libraries
- CORS: Enable requests between the frontend and backend by using cors in the backend: [CORS](https://fullstackopen.com/en/part3/deploying_app_to_internet#same-origin-policy-and-cors), note the expose headers parameter: it's needed to allow the browser to access your custom headers.
- Read the local .env file using [dotenv](https://www.npmjs.com/package/dotenv)
- The backend uses Mongoose to query the database. [mongoose](https://mongoosejs.com/docs/index.html)
- You can use Mongoose's pagination API to bring 10 notes only from Atlas.[Queries with limit example](https://mongoosejs.com/docs/queries.html), [Skip](https://mongoosejs.com/docs/api/query.html#Query.prototype.skip()), if you want to use other libraries, please ask in the forum.
- What is [express](https://expressjs.com)
- Use [Postman](https://www.postman.com/downloads/) to send HTTP requests to the backend.
- Use [Nodemon](https://www.npmjs.com/package/nodemon) to auto refresh the backend after saving its code.
   
# Project parts
## Database
### Mongo server
- It's recommended to use the atlas mongodb to store the notes. Open a free account and initialize a new database in Atlas. For more details, follow the example from [Saving data to MongoDB- MongoDB](https://fullstackopen.com/en/part3/saving_data_to_mongo_db#mongo-db). 
- You can also use a [local mongo database](https://www.mongodb.com/docs/manual/installation/). 
- The University's firewall prevents connection with Atlas. You can use hotspot from your phone to work around it.
- The mongodb URL is inside the a .env file. It also contains the password for access, so we don't share the .env file. We will test using our own .env.
- .env will include `MONGODB_CONNECTION_URL`. For example:  
```
  MONGODB_CONNECTION_URL="mongodb+srv://<username>:<password>@cluster0.r1sudcq.mongodb.net/test?retryWrites=true&w=majority&appName=Cluster0"
```
  This URL will be used by Mongoose to connect to MongoDB.

### Model description:
- Unlike HW1, you can no longer assume that Mongodb will always contain at least one note. It can be empty.
- Instead of `id` , we use mongo's `_id` field instead. 
- Similarly to fullstackopen, the collection name is `notes,` and the schema will match the `note` structure:
```json
{
 title: string;
 author: {
 name: string;
 email: string;
 } | null;
 content: string;
};
```

## Backend
### Backend Requirements
- The backend must use `dotenv` to load the mongodb connection variable (and maybe more) from `./backend/.env`.

- **Routes**
   - All routes must be defined in the router and handled by the appropriate controller functions.
   - See HTTP Status Codes section below.
   - The following RESTful endpoints should be implemented:

     | Method | Endpoint                   | Description                          | Return Value                  | Possible Error Codes           |
     |--------|----------------------------|--------------------------------------|-------------------------------|--------------------------------|
     | GET    | `/notes`                   | Get notes of a selected page, by parsing _page,_per_page query params (details below).        | `200 OK` with array of notes | `500 Internal Server Error`    |
     | GET    | `/notes/:id`               | Get note by MongoDB `_id`            | `200 OK` with note JSON       | `404 Not Found`, `500`         |
     | POST   | `/notes`                   | Create a new note                    | `201 Created` with new note   | `400 Bad Request`, `500`       |
     | PUT    | `/notes/:id`               | Update note by MongoDB `_id`         | `200 OK` with updated note    | `400`, `404`, `500`            |
     | DELETE | `/notes/:id`               | Delete note by MongoDB `_id`         | `204 No Content`              | `404`, `500`                   |
     | GET    | `/notes/by-index/:i`       | Get the _i'th_ note by index         | `200 OK` with note JSON       | `404`, `500`                   |
     | PUT    | `/notes/by-index/:i`       | Update the _i'th_ note               | `200 OK` with updated note    | `400`, `404`, `500`            |
     | DELETE | `/notes/by-index/:i`       | Delete the _i'th_ note               | `204 No Content`              | `404`, `500`                   |

- **Middleware Logging**
   - Implement a middleware that logs every incoming HTTP request to `log.txt`.
   - Each log entry must include:
     - Timestamp
     - HTTP method
     - Target route
     - Request body (if applicable)

- **Note Order**
   - Notes returned from `/notes` must be sorted by MongoDB `_id` (newest first).
```js
db.collection.find().sort({ _id: -1 })  // Newest first
```
- **Backend response for GET /notes**
  - The backend should have the same format as the json-server. The frontend should be able to work with both of them without code changes.
    - the total amount of notes are in the reponse header `X-Total-Count`
    - and the return format is identical to json-server's format.


- **Data transfer limit**
    - We always get no more than 10 notes in a single mongodb request. Which 10 is decided by parsing URL query parameters sent by the frontend. Look for `req.query` in [express docs](https://expressjs.com/en/api.html#req).

- **Backend - Jest:** 4 tests, one for each action in CRUD, write them in `crud.test.ts`. Your test should pass after running `npm run test` from the backend directory. Notice that the test runs the backend server by default.
- **Package.json should support**
```json
  "scripts": {
    "start": "node server.js",
    "dev": "NODE_ENV=dev nodemon server.js",
    "test": "jest"
  },
```
`NODE_ENV` allows to enable special routes for development only: for example, delete all notes. Then, you can use:
```js
if (process.env.NODE_ENV === 'dev'){
  app.use('/test',testRouter)
}
```


### Backend structure

Single Responsibility Principle:
  Each module or file should have one clearly defined task, making the code easier to develop, and less likely to become a mess which will slow you down.

An incoming frontend request will move through a the following, in this order: middleware, router, controller, service and model. It is strongly related to Model-View-Controller model (MVC).

- **Middleware**: Functions that usually run before a request hits a route, often used for logging, authentication, or error handling.

- **Router**:  Defines the application's HTTP endpoints and maps them to functions, called controllers.

- **Controller**: Handles incoming requests, validates inputs, and delegates work to services.

- **Service**:Contains the core "business" (that is not related to the code infrastructure) logic and communicates with the database through models.

- **Model**:  Defines the data schema and provides methods to interact with the database, typically using Mongoose.

### Minimal expected directory structure
```
.
├── backend
	├── config/                  # MongoDB connection setup
	├── consts.ts                # Global constants
    ├── package.json             
	├── controllers/
	│   └── ...                  # Handles HTTP logic, validates input
	├── expressApp.ts            # Sets up Express with routes and middleware
	├── log.txt                  # Output file for logs
	├── middlewares/
	│   └── ...                  # Logs incoming requests
	├── models/
	│   └── ...                  # Mongoose schema for Note
	├── routes/
	│   └── ...                  # Maps endpoints to controller functions
	├── server.ts                # Uses expressApp and starts the Express server (listen())
	├── services/
	│   └── ...                  # Business logic (e.g., DB operations)
	├── tests/
	│   └── crud.test.ts         # Jest backend tests for CRUD
	└── utils/                   # Utilities: file, string, HTTP requests handlers. 
  ```
expressApp and server are separated for flexibility and reusability.

## Frontend
### Frontend Description:
- The frontend communicates with the backend with Axios HTTP requests.
- The UI will support Create, Read, Update and Deletion (CRUD) of nodes. details below.
- Like before, each page has ten notes. The new backend is now responsible for fetching them instead of json-server.
- The frontend and backends should have a single source of truth: the front end should not diverge from the database after a write operation.

### Minimal Expected directory structure
```
.
├── frontend
	│   ├── package.json
	│   ├── playwright-tests
	│   │   └── test.spec.ts 	 # playwright tests
	│   ├── playwright.config.ts #playwright config
	│   ├── src
	│   │   ├── components       #just for components
	│   │   ├── contexts         #context files
```
### Frontend code requirements
- **Reducer and Context:** Use the reducer and context pattern from [Scaling Up with Reducer and Context](https://react.dev/learn/scaling-up-with-reducer-and-context).
- **Playwright:** 4 tests, one for each action in CRUD, write them in `frontend/playwright-tests/test.spec.ts`. Your test should pass after running `npm run test` from the frontend directory. Notice that playwright does not run the server by default.

#### Example playwright config
```json
import { defineConfig } from '@playwright/test';
export default defineConfig({
  testDir: './playwright-tests',
  timeout: 3000,
  use: {
    headless: true,
  },
});
```

### Frontend unit testing requirements
- We align with with the standard testing attribute, (data-testid)[https://playwright.dev/docs/locators#locate-by-test-id].
- Each note should have an `Edit`/`Delete` button. The delete is straightforward, and the edit will make the text inside the note editable. Specifically:
    - Edit button: 
        - data-testid attribute: **"edit-<note_id>"**.
        - When clicked:
            - an editable text input should be rendered, with the same input as the note. with name attribute **"text_input-<note_id>"**.
            - a new button appears: a "save" button **"text_input_save-<note_id>"**.
            - a new button appears: with "cancel" button **"text_input_cancel-<note_id>"**. 
    - Delete button:
        - name attribute **"delete-<note_id>"**. 
        - when clicked, the frontend updates by refreshing the same page.
        - we will not test the scenario in which a delete operation empties the current page (and then we might need to change a page).
- Update notes: for consistency with Playwright standards, please replace the note's `id` HTML attribute  with `data-testid`. 
  #### Example expected HTML:
    ```html
    <div class="note" data-testid="68160458f7eef8a0cfc10902">
        <h2>new note</h2>
        <small>By Danny</small>
        Test note 
        <button data-testid="delete-68160458f7eef8a0cfc10902">Delete</button>
        <button data-testid="edit-68160458f7eef8a0cfc10902">Edit</button>
    </div>
    ```
  After clicking Edit:
  ```html
  <div class="note" data-testid="68160458f7eef8a0cfc10902">
    <h2>new note</h2>
    <small>By Danny</small>
    Test note
    <button data-testid="delete-68160458f7eef8a0cfc10902">Delete</button>
    <textarea data-testid="text_input-68160458f7eef8a0cfc10902">Test note</textarea>
    <button data-testid="text_input_save-68160458f7eef8a0cfc10902">Save</button>
    <button data-testid="text_input_cancel-68160458f7eef8a0cfc10902">Cancel</button>
  </div>
  ```


- `Add new note` button: we will only test the body of the new note, (content field). (Update: appears in the first page or all pages).
    - button name attribute **"add_new_note"**.
    - When clicked, React should render a text input:
        - It should be editable
        - with name attribute **"text_input_new_note"**
        - similarly to edit: a "save" button appears, with name attribute **"text_input_save_new_note"**, and a "cancel" button, with name attribute **"text_input_cancel_new_note"**.
      #### Example Expected HTML (after clicking)
      ```html
      <div>
        <input type="text" value="" name="text_input_new_note">
        <button name="text_input_save_new_note">Save</button>
        <button name="text_input_cancel_new_note">Cancel</button>    
      </div>
      ```
- `Notification area`, text status for the user:
    - class name attribute: **"notification"**. For example: `<div class="notification">Notification area</div>`
    - Its content should be one of four text options: 
        - Initially: `Notification area`
        - After a succesful add/delete/update: `Added a new note`, `Note deleted`, `Note updated`

# Misc
## The tester will:
- `git clone <your_submitted_github_repo>`
- `cd <cloned_dir>`
- `git checkout submission_hw2`
- `npm install` from the `frontend` dir (package.json should exist)
- `npm run dev` from the `frontend` dir  (configured to default port 3000)
- Copy a `.env` file into the `backend` dir.
- `npm install` from the `backend` dir (package.json should exist)
- `node index.js` from the `backend` dir (configured to default port 3001)
- Run tests: frontend (playwright) and backend (jest).

## Example launch.json
Might help to set up debug:
```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Debug backend",
            "type": "node",
            "request": "launch",
            "runtimeExecutable": "npm",
            "runtimeArgs": ["run", "test"],
            "cwd": "${workspaceFolder}/backend",
            "console": "integratedTerminal",
            "internalConsoleOptions": "neverOpen"
          }
    ]
}
```

## Recommendation: health check for backend
see if the backend is running:
```js
app.get('/health', (req, res) => res.send('OK'));
```


## Recommendation: single error handling in a middleware and express-async-errors:
Place this after all routes and middlewares in your main server file:
```ts
import { Request, Response, NextFunction } from 'express';

app.use((err: any, req: Request, res: Response, next: NextFunction) => {
  const status = err.status || 500;
  res.status(status).json({ error: err.message || 'Internal Server Error' });
});
```

Now you can write async routes without try/catch, and errors will still be caught:
```ts
import 'express-async-errors';

app.get('/notes/:id', async (req: Request, res: Response) => {
  const note = await Note.findById(req.params.id);
  if (!note) {
    throw new Error('Note not found'); // This will still be caught
  }
  res.json(note);
});


```

Afterwards, this code
```ts
export const getAllNotes = async (req: Request, res: Response) => {
  try {
    const { notes, count } = await noteService.getAllNotes(req.query);
    res.set('X-Total-Count', count);
    res.status(200).json(notes);
  } catch (error) {
    res.status(500).json({ message: 'Error fetching notes', error });
  }
};

```
turns to this shorter, cleaner code:
```ts
export const getAllNotes = async (req: Request, res: Response) => {
  const { notes, count } = await noteService.getAllNotes(req.query);
  res.set('X-Total-Count', count);
  res.status(200).json(notes);
};
```

## Separation of concerns:
You can develop the backend in parallel and separately from the frontend, by using Postman to send calls to your server before the frontend is ready.
Similarly, you can develop the frontend before the backend is ready with json-server.   

## HTTP Status Codes
Your backend should respond with appropriate HTTP status codes according to the type and outcome of each request. Refer to [MDN's HTTP Status Code Reference](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status) for more details.

### Common Error Codes
- **400 Bad Request** – The client sent an invalid request (e.g., missing required fields in the body).
- **404 Not Found** – The requested resource (e.g., a note by ID or an unknown route) does not exist.
- **500 Internal Server Error** – Should not happen. It's when A server-side error occurred (e.g., database failure).

### Success Codes
- **200 OK** – The request succeeded (e.g., retrieving a note, updating a note).
- **201 Created** – A new resource was successfully created (e.g., a new note).
- **204 No Content** – The resource was successfully deleted; no content is returned in the response.

## Recommended reading
- Most of the material can be found in [Full Stack Open- part 3](https://fullstackopen.com/en/part3).	
- MVC (Model-View-Controller) (https://developer.mozilla.org/en-US/docs/Glossary/MVC)
- Git:
  - Timeline View: https://code.visualstudio.com/docs/getstarted/userinterface#_timeline-view
  - Viewing Diffs: https://code.visualstudio.com/docs/sourcecontrol/overview#_viewing-diffs
- React dev tools : https://react.dev/learn/react-developer-tools
- website design for fun: [tailwind play](https://play.tailwindcss.com) + [design samples with code](https://www.hyperui.dev/components/application)
- SRP [The Single Responsibility Principle](https://blog.cleancoder.com/uncle-bob/2014/05/08/SingleReponsibilityPrinciple.html) – A key concept for designing clean, testable components.

## Common attribute Usage
- Since `class` is a reserved word in JS, so react uses `className` when it needs the `class` HTML attribute.
- `data-testid` is used by [playwright's getByTestId](https://playwright.dev/docs/locators#locate-by-test-id) for identifying test elements.
- `name` attribute is used for naming elements, especially in form submits. 
- `class` attribute is mainly used for css styling.
  - [Mozilla Web Docs- Attributes](https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Attributes)

## Test-Driven Development (TDD)
Test-Driven Development (TDD) is a software development approach where tests are written before writing the actual implementation code. This exercise is a good opportunity to try this method.
- [A recent talk by the Creator of TDD](https://youtu.be/C5IH0ABmyc0?t=1306) – A session explaining the principles and motivation behind TDD: you can skip straight to the practical part.

## .gitignore partial example
```bash

# dependencies
**/node_modules

# local env files
.vscode/

backend/log.txt
backend/.env
backend/package-lock.json
frontend/package-lock.json
```

## Good luck!

