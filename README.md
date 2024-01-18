## Challenge

Design the component API for all the permutations outlined in the image

<img src="https://github.com/victorgaard/ashby-challenge/assets/13384559/545e0eba-884f-4d50-8677-fd457492e986" width="500px" height="auto" />

## Assumptions

- Components must be easy to use both by engineers that value flexibility as well as by engineers who prefer good constraints.
- Composability over props.
- Dropdown is a pure UI component. All logic and function callbacks should be handled in the parent component.
- If a subcomponent is used where it shouldn't be, an error is thrown displaying which subcomponents are supported.

## Inspiration:

Radix Primitives' component composition inspired my approach.

## Components:

`Dropdown`:

- Dropdown is the main component and holds all the subcomponents.
- It can be used in a controlled or uncontrolled manner
- Uncontrolled: the dropdown manages itself when it's opened and when it's closed
- Controlled: developers need to manage the open and closed states. This is done by passing a boolean to `isOpen` and providing a function to `onOpenChange`.
- When to use controlled: when there is a need for fine-grained controls, i.e.: one of the dropdown items onClick handles a promise and needs it resolved before closing the dropdown
- Only accepts `Dropdown.Trigger` and `Dropdown.Body` as children, else it throws an error

- `Dropdown.Trigger`:

  - Contains the trigger button that toggles the dropdown. It accepts any content as children, except `TextInput`
  - If the custom content is a button or an anchor, `asChild` should be passed as true so the trigger props can be passed down to the custom content instead
  - Receives `isOpen` via context, alongside either an internal or custom `onOpenChange` to update it

- `Dropdown.Body`:

  - Contains the dropdown body and its style.
  - Receives `isOpen` via context and renders the dropdown body.
  - Only accepts `Dropdown.Search`, `Dropdown.Group`, `Dropdown.Item`, `Dropdown.Separator`, and `Dropdown.Extra`, as children, else it throws an error

- `Dropdown.Search`:

  - Contains the search wrapper style, and renders a `TextInput`
  - Requires a search string, and an `onSearchChange` function

- `Dropdown.Group`:

  - Contains the group wrapper style
  - Requires a `groupBy`` string
  - Only accepts `Dropdown.Item` as children

- `Dropdown.Item`:

  - Renders the dropdown item
  - Requires an `onClick` function
  - Clicking in an item closes the dropdown, but the event is passed as an optional argument in `onClick`` function in case the behavior should be changed

- `Dropdown.ItemImage`:
  - Renders the item image

- `Dropdown.ItemTitle`:
  - Renders the item title

- `Dropdown.ItemSubTitle`:
  - Renders the item subtitle

- `Dropdown.Separator`:

  - Renders a line separator

- `Dropdown.Extra`:
  - A component to render anything, like a button

## Pseudo JSX:

```HTML
// Developers can make the Dropdown controlled, but it's not required
<Dropdown isOpen={boolean?} onOpenChange={(isOpenStatus) => void(isOpenStatus)?}>
  // When asChild is set to true, the trigger props will be passed down to the custom content
  <Dropdown.Trigger asChild={boolean?}>
    // Any content, except TextInput
  <Dropdown.Trigger>

  <Dropdown.Body>
    // Optional search component 
    <Dropdown.Search search={string} onSearchChange={(newSearch) => void(newSearch)} />

    // Renders the item
    <Dropdown.Item onClick={(event?) => void}>
      // Optional item image
      <Dropdown.ItemImage src={string} />

      // Required item title
      <Dropdown.ItemTitle>
        // Text
      </Dropdown.ItemTitle>

      // Optional subtitle
      <Dropdown.ItemSubtitle>
        // Text
      </Dropdown.ItemTitle>
    </Dropdown.Item>

    // Optional way to group results
    <Dropdown.Group groupBy={string}>
      <Dropdown.Item onClick={(event?) => void}>
        ...
      </Dropdown.Item>
    </Dropdown.Group>

    // Optional section separator
    <Dropdown.Separator />

    // Optional extra content
    <Dropdown.Extra>
      // Anything can go in here, like a button
    </Dropdown.Extra>
  </Dropdown.Body>
</Dropdown>
```
