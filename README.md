## Challenge

Design the component API for all the permutations outlined in the image

<img src="https://github.com/victorgaard/ashby-challenge/assets/13384559/545e0eba-884f-4d50-8677-fd457492e986" width="500px" height="auto" />

## Assumptions

- Components must be easy to use both by engineers that value flexibility as well as by engineers who prefer good constraints.
- All logic, data fetch, data transformation, and function callbacks should be handled in the parent component that is using the Dropdown.

## Inspiration:

Radix Primitives' component composition inspired my approach.

## Components:

`Dropdown`:

- Dropdown is the main component and holds all the subcomponents
- Requires `isOpen` and `onDropdownToggle`
- Has a context in which `isOpen` and `onDropdownToggle` are shared between subcomponents

- `Dropdown.Trigger`:

  - Contains the trigger button that toggles the dropdown
  - It accepts any kind of children, except `<TextInput />`
  - If renders a `<Button>` with the children inside
  - When the trigger is clicked, `onDropdownToggle` is called
  - If the children is either a button or an anchor, `asChild` should be marked as true. This is a way for the trigger props to be forwarded to the custom content instead

- `Dropdown.Body`:

  - Renders the dropdown body and its children when `isOpen` is `true`.
  - Holds the dropdown body style

- `Dropdown.Search`:

  - Contains the search wrapper style, and renders a `<TextInput>` as a controlled component
  - Requires a string `value`, and an `onChange` function to manage `<TextInput>` states

- `Dropdown.Group`:

  - Contains the group wrapper style
  - Requires a `groupBy` string

- `Dropdown.Item`:

  - Renders the dropdown item and manage its style based on the props
  - Requires a `title`, and an `onClick` function
  - Optinally it can receive a `subtitle`, and a `picture`
  - Clicking in an item closes the dropdown, but the event is passed as an optional argument in `onClick` function in case the behavior needs to be changed

- `Dropdown.Separator`:

  - Renders a line separator

- `Dropdown.Skeleton`:

  - Renders a loading skeleton

- `Dropdown.Footer`:
  - A component to render any children, like the control to add a new item

## Pseudo JSX:

```HTML
// Developers need to specify when the dropdown is open or not.
// Additionally, they need to provide a close function
<Dropdown isOpen={boolean} onDropdownToggle={() => void}>
  // When using a button or an anchor as the trigger, asChild
  // needs to be set to true, so the props will be forwarded
  // to the custom content
  <Dropdown.Trigger asChild={boolean?}>
    // Any content, except TextInput
  <Dropdown.Trigger>
  <Dropdown.Body>
    // Optional search component
    <Dropdown.Search searchTerm={string} onSearchTermChange={(search) => void} />

    // Optional results loading skeleton when search is connected to an API,
    // or initial results haven't loaded yet
    <Dropdown.Skeleton />

    // Renders the result item and expect a title and an onClick handler
    // When clicked, it executes the callback and close the dropdown
    <Dropdown.Item onClick={(event?) => void} title={string} subtitle={string?} picture={string?} />

    // Optional way to group result items
    // A groupBy string is required
    <Dropdown.Group groupBy={string}>
      <Dropdown.Item ... />
    </Dropdown.Group>

    // Optional separator
    <Dropdown.Separator />

    // Optional footer
    <Dropdown.Footer>
      // Any content can go here
    </Dropdown.Footer>
  </Dropdown.Body>
</Dropdown>
```
