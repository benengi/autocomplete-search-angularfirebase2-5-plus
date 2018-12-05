Quick update to Angular 7+.

Old version at https://github.com/audiBookning/autocomplete-search-angularfirebase2-5-plus/tree/original (ATTENTION: many packages are not updated and unsecures)

### Refactoring of the code of '_Autocomplete Search With Angular4 and Firebase_' at https://angularfirebase.com/lessons/autocomplete-search-with-angular4-and-firebase/ for AngularFire 2 (https://github.com/angular/angularfire2) version 5.+

Default project generated with:

- Angular CLI: 7.1.1
- Node: 10.12.0
- Angular: 7.1.0
- typescript: 3.1.6
- @angular/fire (angularfire2): 5.1.1
- firebase: 5.6.0
- rxjs: 6.3.3

Do not forget to add your Firebase Credentials in the file at `.\src\app\firebase.credentials.ts`

I tried to follow the original code the best i could, but some things had to be changed nonetheless.

- `movies.service.ts`:

  - Added `start.switchMap` to get the string value of the `BehaviorSubject`. I had some problems if used directly.
  - Used `snapshotChanges()` instead of `valueChanges()` to keep the firebase key in the data by adding an extra `map` that return a new object `{ key: c.payload.key, ...c.payload.val() };` Refer to https://github.com/angular/angularfire2/blob/master/docs/rtdb/lists.md for more info.
  - Added `.debounceTime(200)` and `.distinctUntilChanged()` operators.
    - `debounceTime(200)` to avoid to hit the database after every keypress, replacing the code in the chapter '_Preventing Multiple Queries to Firebase_' of the article mentioned earlier.
    - `distinctUntilChanged()` for the cases when, for ex, one type 'mov' wait for the observable to resolve then type 'movi' followed rapidly by deleting the last keypress getting 'mov' and avoiding another hit to firebase.
  - Used the RxJs operator `BehaviorSubject` instead of `Subject` to initialize the query on first load.
  - Corrected `getMovies` method to use only one argument `start` since `end` is directly dependent of `start`. One can avoid having 2 `BehaviorSubject` and having to use alternatively the RxJs operator `zip` to synchronize them (See the answers in the article for an example of the use of the `zip` operator).

- `movie-search.component.html` (including also the changes in `movie-search.component.ts`):
  - Changed the input to `#searchInput (keyup)="search(searchInput.value)`
    - Added `#searchInput` following best practices from _Get user input from a template reference variable_ (https://angular.io/guide/user-input#get-user-input-from-a-template-reference-variable)
    - `(keyup)` instead of `(keydown)` because it was giving me some weird synchronization problems.
    - In this simple example, one could further simplify the code by replacing `(keyup)="search(searchInput.value)"` with `(keyup)="startAt.next(searchInput.value)"` and deleting the `search()` method.
  - Converted the `movies` array to an observable `movies$` and used the `async` pipe to follow some Reactive Programming best practices, simplifying the code and handling the unsubscription automatically

---

Autogenerated by Angular CLI:

---

# AutocompleteSearchAngularfirebase

This project was generated with [Angular CLI](https://github.com/angular/angular-cli) version 7.1.1.

## Development server

Run `ng serve` for a dev server. Navigate to `http://localhost:4200/`. The app will automatically reload if you change any of the source files.

## Code scaffolding

Run `ng generate component component-name` to generate a new component. You can also use `ng generate directive|pipe|service|class|guard|interface|enum|module`.

## Build

Run `ng build` to build the project. The build artifacts will be stored in the `dist/` directory. Use the `--prod` flag for a production build.

## Running unit tests

Run `ng test` to execute the unit tests via [Karma](https://karma-runner.github.io).

## Running end-to-end tests

Run `ng e2e` to execute the end-to-end tests via [Protractor](http://www.protractortest.org/).

## Further help

To get more help on the Angular CLI use `ng help` or go check out the [Angular CLI README](https://github.com/angular/angular-cli/blob/master/README.md).
