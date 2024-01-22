## Challenge

Design the component API for all the permutations outlined in the image

<img src="https://github.com/victorgaard/ashby-challenge/assets/13384559/545e0eba-884f-4d50-8677-fd457492e986" width="500px" height="auto" />

## Assumptions

- Components must be easy to use both by engineers that value flexibility as well as by engineers who prefer good constraints.
- All logic, data fetch, data transformation, and function callbacks should be handled in the parent component that is using the Dropdown.

## Inspiration:

Radix Primitives' component composition inspired my approach.

## Design rationale

- I chose to build my component using the compound pattern to aim for modularity and customization
- The base component and its subcomponents are the building blocks to compose a Dropdown in multiple ways (think it like a LEGO box)
- Component wrappers can be made composing certain subcomponents to tailor for the use cases (i.e.: `DropdownAutocomplete`, `DropdownUsers`, `DropdownGroupBy`, etc)
- Benefit for design engineers: they can easily maintain and improve the component without relying in complex optionals or logic to handle all use cases. 
- In case there are new requirements for the Dropdown, iterating existing subcomponents or creating other wrappers is straightforward
- Benefit for product engineers: they can opt-in to use a wrapper component instead of composing with the subcomponents
- Lastly, in my opinion, testing, debugging and iterating components this way becomes simpler than a one size fits all

## Tradeoffs
- This approach adds initial overhead to the process, so the buy in from the team is crucial
- It requires an exhaustive set of subcomponents, and the creation of wrapper component variants to support the use cases
- Furthermore, it also relies on good documentation


## Pseudo JSX:

- Base component

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

- Derived wrapper component for a dropdown with in-memory search (all wrappers should be paired with tests to catch any breaking changes early on)

```JSX
function DropdownAutocomplete({ trigger, results, onClick }: DropdownAutocompleteProps) {
  const [isOpen, setIsOpen] = useState(false)
  const [searchTerm, setSearchTerm] = useState("")

  const filteredResults = results.filter(
    result =>
      title.toLowerCase().includes(searchTerm.toLowerCase())
  );

  return (
    <Dropdown isOpen={isOpen} onDropdownToggle={() => setIsOpen(!isOpen)}>
      <Dropdown.Trigger>
        {trigger}
      </Dropdown.Trigger>
      <Dropdown.Body>
        <Dropdown.Search searchTerm={searchTerm} onSearchTermChange={search => setSearchTerm(search)} />
        {filteredResults.map(result => <Dropdown.Item key={result.id} title={result.title} onClick={onClick} />)}
      </Dropdown.Body>
    </Dropdown>
  )
}
```

- Wrapper usage
```JSX
<DropdownAutocomplete trigger={<Button>Click me</Button>} results={results} onClick={() => void} />
```

## Component blueprint:

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
