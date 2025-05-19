# Conduit

This a full-stack TypeScript application consisting of a NestJS backend and an Angular frontend.

Conduit is a social blogging site (i.e. a Medium.com clone). It uses a custom API for all requests, including authentication.

# features



General functionality:

    Authenticate users via JWT (login/signup pages + logout button on settings page)
    CRU- users (sign up & settings page - no deleting required)
    CRUD Articles
    CR-D Comments on articles (no updating required)
    GET and display paginated lists of articles
    Favorite articles
    Follow other users

<p></p>
<p></p>
</br>

#Screenshot
<p></p>
<p></p>
</br>

![Screenshot 2025-05-19 011627](https://github.com/user-attachments/assets/eb8c3562-f579-4d73-ba1d-4f15280d08c6)

<p></p>
<p></p>
</br>
<p></p>
<p></p>
</br>
The codebase is the `the-conduit-roster` project. It is structured as an Nx monorepo, which houses applications, libraries, and configurations for modular development and scalability. 

Key technologies include: Angular, NestJS, MikroORM, NodeJS, MySQL, NgRX, REST.

Key directories include:
- `apps`: Hosting core applications: the Angular frontend and NestJS backend.
- `libs`: Shared libraries for cross-application use.
- `tools`: Scripts and utilities for workspace management.

---

For this application, this is the Angular data flow for the article editing process:

The `feature-article-edit` directory contains the `ArticleEditComponent`, which seems to be responsible for editing articles. The component consists of:

1. **article-edit.component.ts**: The TypeScript logic for the component.
2. **article-edit.component.html**: The HTML template, likely containing the form for editing.
3. **article-edit.component.css**: The styling for the component.

The `ArticleEditComponent` is the component responsible for editing articles. Let's break down its key aspects:

1. **Dependencies and Imports**:
   - Imports from `@realworld/core/forms` provide utilities for dynamic forms, such as `DynamicFormComponent` and `Field`.
   - NgRx's `Store` is imported, which is the primary mechanism to interact with the state.
   - Various actions and selectors related to articles are imported.

2. **Form Structure (`structure`)**:
   - The form structure is defined as an array of `Field` objects. Each object describes a form field, such as its type (`INPUT`, `TEXTAREA`), name, placeholder, and validation rules.

3. **Component Decorator and Members**:
   - The `@Component` decorator sets up the component's metadata.
   - `structure$` and `data$` are observables that select specific slices of the state related to the form's structure and data.
   - The `store` is injected, which is an instance of NgRx's `Store`.

4. **Lifecycle Hooks**:
   - `ngOnInit`: On initialization, the form structure is set using the `formsActions.setStructure` action. It also subscribes to the article data and sets it as the form's data using the `formsActions.setData` action.
   - `ngOnDestroy`: When the component is destroyed, it initializes the form using the `formsActions.initializeForm` action.

5. **Event Handlers**:
   - `updateForm(changes: any)`: When the form's data is updated, it dispatches the `formsActions.updateData` action with the new data.
   - `submit()`: On form submission, it dispatches the `articleEditActions.publishArticle` action, signaling the intent to save the changes to the article.

From this component:

- **UI to Store**: The `updateForm` method captures changes in the form and updates the store with the new data.
- **Store to Backend**: The `submit` method signals the intent to publish (or save) the article. This will be captured by an effect that makes the actual API call.

The `articleEditActions` includes two actions:

1. **publishArticle**: This action is dispatched when the user submits the form to save changes to an article. It doesn't carry a payload, indicating it's more about triggering the process.
2. **publishArticleSuccess**: This action is dispatched upon successful article publication. Like the previous action, it doesn't carry additional data, suggesting it's used to signal completion.

The `publishArticle$` effect is crucial in handling the side effects of the `publishArticle` action:

1. **Trigger**:
   - This effect listens for the `publishArticle` action.

2. **Data Fetching**:
   - It uses `concatLatestFrom` to combine the latest form data from the state (`ngrxFormsQuery.selectData`).

3. **API Call**:
   - The combined stream ([action, form data]) is processed with `concatMap`, where it calls `articlesService.publishArticle(data)` to send the updated article data to the backend.

4. **Post API Call Actions**:
   - On successful update, it navigates to the updated article using `router.navigate(['article', result.article.slug])`.
   - Dispatches `publishArticleSuccess` to signal the successful update.
   - In case of an error, it dispatches `formsActions.setErrors` with the error details.

Data Flow Summary for Editing an Article:

1. **User Action**: The user edits the article and submits the form.
2. **UI to Store**: The `ArticleEditComponent` dispatches `publishArticle` action.
3. **Store to Backend (via Effect)**:
   - The `publishArticle$` effect catches this action.
   - It fetches the current form data from the state.
   - Makes an API call through the `ArticlesService`.
   - Upon success or failure, dispatches appropriate actions.
4. **State Update**: The reducer updates the state based on the result (handled by actions dispatched from effects).
5. **UI Update**: The UI components subscribed to the state reflect the updated state.

---



## How to deploy

1. [Start the code in Gitpod](#running-in-gitpod)


## Running in Gitpod

You can sign up for a free Gitpod account here: https://www.gitpod.io/ using your GitHub account. After you sign up, open the following link to launch a browser-based VSCode environment: https://www.gitpod.io/#https://github.com/engprodigy/the-conduit-roster.git . You may refer to the [running in Gitpod](./GITPOD.md) doc for hints about using Gitpod.

This project can also be [run locally](./LOCAL.md); this will require up to 60 minutes of set-up time. We only recommend using this if you have technical impediments in using Gitpod.

Once the project is running, you can access the UI at http://localhost:4200 and log in with `jcosten0@purevolume.com` / `password`. You can also find the backend API spec at http://localhost:3000/docs.
