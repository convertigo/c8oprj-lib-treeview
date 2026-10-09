


# lib_TreeTable

Reusable Convertigo NGX library exposing a `treeTable` shared component that wraps [PrimeNG TreeTable v20](https://primeng.org/treetable) (primeng 20.1.0 on Angular 20) to display and interact with hierarchical data in any NGX application.

## Using this library in your application

1. In your application project, add a Project reference to `lib_TreeTable`.
2. In a page, drop a **Use shared component** targeting `lib_TreeTable.Application.NgxApp.treeTable`.
3. At minimum bind `value` (tree data) and `columns` (column definitions). Every other input is optional.

Minimal setup (Use shared component variables):

| Variable | Example value |
|---|---|
| value | `this?.local?.treeData` |
| columns | `this?.local?.treeColumns` |
| selectionMode | `'single'` |

```ts
page.local.treeData = [
  {
    data: { name: 'Documents', size: '—', type: 'folder' },
    expanded: true,
    children: [
      { data: { name: 'Resume.docx', size: '48 Ko', type: 'word' } }
    ]
  }
];
page.local.treeColumns = [
  { header: 'Name', field: 'name' },
  { header: 'Size', field: 'size' },
  { header: 'Type', field: 'type' }
];
```

## Data model

`value` expects an array of PrimeNG `TreeNode` objects:

- `data`: object holding the row payload, one property per column `field`.
- `children`: array of child nodes (any depth).
- `expanded`: optional initial expansion state.
- `leaf`: optional hint to hide the expander for empty folders.

## Column definition

`columns` expects an array of column objects:

| Property | Type | Default | Description |
|---|---|---|---|
| header | string | — | Header label (rendered empty on checkbox columns). |
| field | string | — | Property of `node.data` displayed in the cell. |
| width | string | null | Optional fixed column width (CSS value such as `'200px'` or `'54%'`), applied to the header and body cells. |
| sortable | boolean | true | Set `false` to disable header sorting on this column. |
| resizable | boolean | true | Set `false` to disable resize on this column (requires `resizableColumns=true`). |
| reorderable | boolean | false | Set `true` to allow drag-and-drop reorder of this column (requires `reorderableColumns=true`). |
| type | string | — | `'checkbox'` renders the header select-all checkbox and the row selection checkboxes. `'actions'` renders the action buttons defined in `actions` (see below). |
| actions | Action[] | — | Action button definitions, only used when `type` is `'actions'`. |

The expand/collapse toggler is always rendered in the first column.

## Action buttons column

A column with `type: 'actions'` renders one Ionic button per `actions` entry in the cell, aligned to the right. Buttons wrap onto several lines when the column is too narrow, so they never overflow the table.

Each action object supports:

| Property | Type | Default | Description |
|---|---|---|---|
| label | string | — | Button label. When omitted, the button renders as an icon-only button. |
| title | string | — | Tooltip, also used as the action name in `ActionClick` payloads when no label is set. |
| icon | string | — | Icon name rendered on the left (Ionic icon, e.g. `'eye-outline'`). |
| iconEnd | string | — | Icon name rendered on the right, after the label (e.g. `'chevron-forward-outline'`). |
| iconSize | string | null | Size of the left icon (e.g. `'small'`, `'large'`). |
| iconEndSize | string | null | Size of the right icon; falls back to `iconSize`. |
| iconSlot | string | auto | Overrides the left icon slot. Defaults to `'start'` when a label is set, `'icon-only'` otherwise. |
| iconEndSlot | string | `'end'` | Overrides the right icon slot. |
| color | string | null | Ionic color of the button (`'primary'`, `'secondary'`, `'danger'`, `'medium'`, `'tertiary'`...). |
| fill | string | null | Button fill: `'solid'`, `'outline'` or `'clear'`. |
| size | string | `'small'` | Button size (`'small'`, `'default'`, `'large'`). |
| shape | string | null | Button shape, e.g. `'round'`. |
| expand | string | null | Button expand mode, e.g. `'full'` or `'block'`. |
| strong | boolean | false | Renders the button with a strong (bold) font weight. |
| disabled | boolean | false | Disables the button. |

Icon placement examples:

- Icon on the left: `{label: 'Voir', icon: 'eye-outline', color: 'primary', fill: 'solid'}`
- Icon on the right: `{label: 'Suivant', iconEnd: 'chevron-forward-outline', color: 'primary', fill: 'outline'}`
- Icons on both sides: `{label: 'Défiler', icon: 'play-outline', iconEnd: 'chevron-forward-outline'}`
- Icon only: `{icon: 'download-outline', color: 'medium', fill: 'clear', title: 'Télécharger'}`

Clicking a button emits the `ActionClick` event.

```ts
page.local.actionColumns = [
  { header: 'Name', field: 'name', width: '20%' },
  { header: 'Size', field: 'size', width: '13%' },
  { header: 'Type', field: 'type', width: '13%' },
  {
    header: 'Actions', field: 'actions', type: 'actions', sortable: false, width: '54%',
    actions: [
      { label: 'View', icon: 'eye-outline', color: 'primary', fill: 'solid' },
      { label: 'Delete', icon: 'trash-outline', color: 'danger', fill: 'solid' },
      { icon: 'download-outline', color: 'medium', fill: 'clear', title: 'Download' }
    ]
  }
];
```

## Inputs

### Data and display

| Variable | Type | Default | Description |
|---|---|---|---|
| value | TreeNode[] | `[]` | Hierarchical data to display. |
| columns | Column[] | `[]` | Dynamic column definitions (see above). |
| emptyMessage | string | `'Aucun enregistrement'` | Message shown when there is no data. |
| loading | boolean | `false` | Displays a loader mask over the table. |
| showLoader | boolean | `true` | Whether to show the mask when `loading` is true. |
| loadingIcon | string | `null` | Icon shown on the loading mask. |
| dataKey | string | `null` | Property uniquely identifying a record in data. |
| rowTrackBy | function | `null` | Hook delegated to ngForTrackBy to optimize DOM operations. |

### Selection

| Variable | Type | Default | Description |
|---|---|---|---|
| selectionMode | string | `null` | `'single'`, `'multiple'`, `'checkbox'`, or null for no selection. |
| selection | TreeNode \| TreeNode[] | `null` | Selected node(s), two-way bound and auto-emitted. |
| metaKeySelection | boolean | `false` | Whether ctrl/cmd is required to add a row to the selection. |
| compareSelectionBy | string | `'deepEquals'` | Row comparison algorithm: `'equals'` or `'deepEquals'`. |
| contextMenuSelection | TreeNode | `null` | Right-click selected row, two-way bound. |
| contextMenuSelectionMode | string | `'separate'` | `'separate'` or `'joint'` context menu selection behavior. |

### Sorting

| Variable | Type | Default | Description |
|---|---|---|---|
| sortMode | string | `'single'` | `'single'` or `'multiple'` column sorting. |
| sortField | string | `null` | Field used for default sorting. |
| sortOrder | number | `null` | Default sort direction: `1` ascending, `-1` descending. |
| multiSortMeta | SortMeta[] | `null` | Default sort metas in multiple mode, e.g. `[{field:'name', order:1}]`. |
| defaultSortOrder | number | `1` | Order applied when the user first sorts an unsorted column. |
| customSort | boolean | `false` | Set `true` to sort through the `SortFunction` event instead of the built-in sort. |
| resetPageOnSort | boolean | `true` | Resets the paginator to the first page after sorting. |

### Filtering

| Variable | Type | Default | Description |
|---|---|---|---|
| globalFilter | string | `null` | Reserved for a global search value (see limitations below). |
| globalFilterFields | string[] | `null` | Field names searched by the global filter. |
| filters | FilterMetadata | `null` | External filters, e.g. `{name: {value: 'doc', matchMode: 'contains'}}`. |
| filterMode | string | `'lenient'` | `'lenient'` (match at any level) or `'strict'` (whole subtree must match). |
| filterDelay | number | `300` | Delay in ms before filtering. |
| filterLocale | string | `undefined` | Locale used when filtering. |

### Pagination

| Variable | Type | Default | Description |
|---|---|---|---|
| paginator | boolean | `false` | Enables pagination. |
| rows | number | `null` | Rows per page. |
| first | number | `0` | Index of the first displayed row. |
| rowsPerPageOptions | number[] | `null` | Choices of the rows-per-page dropdown, e.g. `[10,20,50]`. |
| paginatorPosition | string | `'bottom'` | `'top'`, `'bottom'` or `'both'`. |
| alwaysShowPaginator | boolean | `true` | Show the paginator even with a single page. |
| showCurrentPageReport | boolean | `false` | Display the current page report. |
| currentPageReportTemplate | string | `'{currentPage} of {totalPages}'` | Report template with `{currentPage},{totalPages},{rows},{first},{last},{totalRecords}`. |
| pageLinks | number | `5` | Number of page links displayed. |
| showPageLinks | boolean | `true` | Show page links in the paginator. |
| showFirstLastIcon | boolean | `true` | Show first/last page icons. |
| showJumpToPageDropdown | boolean | `false` | Show a jump-to-page dropdown. |
| paginatorLocale | string | `undefined` | Locale used in paginator formatting. |
| totalRecords | number | `undefined` | Total record count for lazy pagination; defaults to `value.length`. |

### Scrolling

| Variable | Type | Default | Description |
|---|---|---|---|
| scrollable | boolean | `false` | Enables horizontal/vertical scrolling. |
| scrollHeight | string | `null` | Viewport height in px or `'flex'`. |
| virtualScroll | boolean | `false` | Load rows on demand while scrolling. |
| virtualScrollItemSize | number | `null` | Row height used in virtual scroll calculations (PrimeNG default 28). |
| virtualScrollDelay | number | `150` | Delay in ms before virtual scroll triggers. |

### Column layout

| Variable | Type | Default | Description |
|---|---|---|---|
| resizableColumns | boolean | `false` | Allow column resize by drag and drop. |
| columnResizeMode | string | `'fit'` | `'fit'` keeps the table width, `'expand'` changes it. |
| reorderableColumns | boolean | `false` | Allow column reorder by drag and drop. |
| showGridlines | boolean | `false` | Show grid lines between cells. |
| autoLayout | boolean | `false` | Cell widths scale to their content. |
| frozenWidth | string | `null` | Width of the frozen columns container, e.g. `'200px'`. |
| frozenColumns | Column[] | `null` | Column definitions rendered in the frozen container (see limitations). |

### Lazy loading

| Variable | Type | Default | Description |
|---|---|---|---|
| lazy | boolean | `false` | Lazy mode: children are loaded through the `LazyLoad` event instead of being fully provided. |
| lazyLoadOnInit | boolean | `true` | Trigger the initial `LazyLoad` when the table is created (only when `lazy=true`). |

### Styling

| Variable | Type | Default | Description |
|---|---|---|---|
| styleClass | string | `null` | Class of the component root element. |
| tableStyle | object | `null` | Inline style object of the table, e.g. `{width:'100%'}`. |
| tableStyleClass | string | `null` | Class of the table element. |

## Events

Every event payload is emitted with the raw PrimeNG event under `event` plus convenience fields.

| Event | Emitted data | Description |
|---|---|---|
| ActionClick | `{action, node, rowData}` | A button of an actions column was clicked (see Action buttons column). |
| NodeSelect | `{event, node}` | A node is selected by row click (`selectionMode` set). |
| NodeUnselect | `{event, node}` | A node is unselected. |
| NodeExpand | `{event, node}` | A node is expanded. |
| NodeCollapse | `{event, node}` | A node is collapsed. |
| SelectionChange | selected node(s) | Selection changed (emitted from the two-way `selection` binding). |
| Page | `{event, first, rows}` | Pagination occurred. |
| Sort | `{event, field, order}` | A column is sorted (built-in sort). |
| SortFunction | `{event, field, order}` | Custom sort requested, when `customSort=true`. |
| Filter | `{event, filters}` | Data has been filtered. |
| LazyLoad | `{event, first, rows}` | Paging, sorting or filtering happened in lazy mode. |
| ColResize | `{event, element, delta}` | A column has been resized. |
| ColReorder | `{event, dragIndex, dropIndex}` | A column has been reordered. |
| ContextMenuSelect | `{event, node}` | A node is selected with a right click. |
| HeaderCheckboxToggle | `{event, checked}` | The header select-all checkbox changed. |
| EditInit | `{event, node}` | A cell switched to edit mode (see limitations). |
| EditComplete | `{event, node}` | A cell edit is completed (see limitations). |
| EditCancel | `{event, node}` | A cell edit is cancelled (see limitations). |

## Demo page

The `testTreeTable` page (labels in French) hosts six use cases of the component, each with its own title:

| # | Use case | Key settings | Demonstrates |
|---|---|---|---|
| 1 | Action buttons in a right column | a `{type:'actions'}` column with `col.width`, `ActionClick` handler | Ionic action buttons (label, icons start/end, icon-only, colors), non-overflowing wrap, ActionClick event. |
| 2 | Single selection with column sorting | `selectionMode='single'` | Row click selection, expand/collapse, sortable headers. |
| 3 | Multiple selection with checkboxes | a `{type:'checkbox'}` column, `selectionMode='checkbox'`, `showGridlines=true` | Row checkboxes, header select-all, gridlines. |
| 4 | Pagination with fixed scroll | `paginator=true`, `rows=2`, `rowsPerPageOptions=[2,5,10]`, `scrollable=true`, `scrollHeight='260px'` | Paginator, rows per page selector, fixed-height scrolling. |
| 5 | Resizable and reorderable columns | `resizableColumns=true`, `reorderableColumns=true`, columns with `reorderable:true` | Column resize and drag-and-drop reorder. |
| 6 | Empty data | `value=[]`, `emptyMessage='Aucun dossier à afficher'` | Custom empty message. |

Demo data is injected by the page `PageEvent` with a realistic file-explorer tree (34 nodes).

## Dependencies

Registered automatically on the shared component host module:

- `primeng` 20.1.0 (`TreeTableModule` from `primeng/treetable`).
- `@primeuix/themes` 2.0.2 with the Aura preset through `providePrimeNG`.

## Notes and limitations

- `globalFilter` is declared but not bound inside the component template; drive global search through the `filters` input or bind your own input to `globalFilterFields`.
- There is no built-in per-column filter UI; provide filters programmatically through `filters`.
- `frozenWidth` and `frozenColumns` are bound as inputs but frozen templates are not rendered in this version, so frozen columns are not displayed yet.
- `EditInit`, `EditComplete` and `EditCancel` are event pass-throughs; no editable cells are rendered by the component templates.
- `ContextMenuSelect` is an event pass-through; the context menu itself must be provided by the hosting page.
- The expand/collapse toggler is always rendered in the first column, regardless of column properties.


For more technical informations : [documentation](./project.md)

- [Installation](#installation)
- [Mobile Library](#mobile-library)
    - [Shared Components](#shared-components)
        - [treeTable](#treetable)


## Installation

1. In your Convertigo Studio click on ![](https://github.com/convertigo/convertigo/blob/develop/eclipse-plugin-studio/icons/studio/project_import.gif?raw=true "Import a project in treeview") to import a project in the treeview
2. In the import wizard

   ![](https://github.com/convertigo/convertigo/blob/develop/eclipse-plugin-studio/tomcat/webapps/convertigo/templates/ftl/project_import_wzd.png?raw=true "Import Project")
   
   paste the text below into the `Project remote URL` field:
   <table>
     <tr><td>Usage</td><td>Click the copy button at the end of the line</td></tr>
     <tr><td>To contribute</td><td>

     ```
     lib_TreeTable=https://github.com/convertigo/c8oprj-lib-treeview.git:branch=main
     ```
     </td></tr>
     <tr><td>To simply use</td><td>

     ```
     lib_TreeTable=https://github.com/convertigo/c8oprj-lib-treeview/archive/main.zip
     ```
     </td></tr>
    </table>
3. Click the `Finish` button. This will automatically import the __lib_TreeTable__ project


## Mobile Library

Describes the mobile application global properties

### Shared Components

#### treeTable

TreeTable shared component wrapping PrimeNG TreeTable v20.

Features:
- [x] Hierarchical data display (TreeNode[] with children)
- [x] Expand/collapse nodes
- [x] Action buttons column (`type: 'actions'` with configurable Ionic buttons)
- [x] Column width (`col.width` applied to header and body cells)
- [x] Single or multiple selection (row click or checkbox)
- [x] Column sorting (single or multiple)
- [x] Client-side filtering (global or per column)
- [x] Pagination
- [x] Scrollable with fixed height or flex
- [x] Virtual scroll for large datasets
- [x] Resizable/reorderable columns
- [x] Lazy loading mode
- [x] Frozen columns
- [x] Row hover, gridlines, auto layout
- [x] Loading state with mask
- [x] All TreeTable output events

Based on PrimeNG 20.1.0 (Angular 20). Documentation: https://primeng.org/treetable

**variables**

<table>
<tr>
<th>name</th><th>comment</th>
</tr>
<tr>
<td>alwaysShowPaginator</td><td>Whether to show the paginator even if there is only one page. Default true</td>
</tr>
<tr>
<td>autoLayout</td><td>Whether the cell widths scale according to their content. Default false</td>
</tr>
<tr>
<td>columnResizeMode</td><td>Whether the overall table width should change on column resize: 'fit' or 'expand'. Default 'fit'</td>
</tr>
<tr>
<td>columns</td><td>Array of column objects. Column shape: {header: 'Name', field: 'name', sortable: true, resizable: true, reorderable: false, width: '200px' or '54%', type: null or 'checkbox' or 'actions', actions: [{label, title, icon, iconEnd, iconSize, iconEndSize, iconSlot, iconEndSlot, color, fill, size, shape, expand, strong, disabled}]}</td>
</tr>
<tr>
<td>compareSelectionBy</td><td>Algorithm to define if a row is selected: 'equals' or 'deepEquals'. Default 'deepEquals'</td>
</tr>
<tr>
<td>contextMenuSelection</td><td>Selected row with a context menu</td>
</tr>
<tr>
<td>contextMenuSelectionMode</td><td>Mode of the context menu selection: 'separate' or 'joint'. Default 'separate'</td>
</tr>
<tr>
<td>currentPageReportTemplate</td><td>Template of the current page report. Placeholders: {currentPage},{totalPages},{rows},{first},{last},{totalRecords}</td>
</tr>
<tr>
<td>customSort</td><td>Whether to use a custom sort via the SortFunction event instead of the default. Default false</td>
</tr>
<tr>
<td>dataKey</td><td>A property to uniquely identify a record in data</td>
</tr>
<tr>
<td>defaultSortOrder</td><td>Sort order used when an unsorted column is sorted by user interaction: 1 or -1. Default 1</td>
</tr>
<tr>
<td>emptyMessage</td><td>Text shown when there is no data to display. Default 'No records found'</td>
</tr>
<tr>
<td>filterDelay</td><td>Delay in milliseconds before filtering the data. Default 300</td>
</tr>
<tr>
<td>filterLocale</td><td>Locale to use in filtering</td>
</tr>
<tr>
<td>filterMode</td><td>Mode for filtering: 'lenient' or 'strict'. Default 'lenient'</td>
</tr>
<tr>
<td>filters</td><td>Array of FilterMetadata objects to provide external filters: {field: {value: x, matchMode: 'contains'}}</td>
</tr>
<tr>
<td>first</td><td>Index of the first row to be displayed (paginator)</td>
</tr>
<tr>
<td>frozenColumns</td><td>Array of column objects that are frozen (same shape as columns)</td>
</tr>
<tr>
<td>frozenWidth</td><td>Width of the frozen columns container, e.g. '200px'</td>
</tr>
<tr>
<td>globalFilter</td><td>Value bound to the TreeTable global filter input (client-side global filtering). Use with globalFilterFields</td>
</tr>
<tr>
<td>globalFilterFields</td><td>Array of field names as string to use in global filtering</td>
</tr>
<tr>
<td>lazy</td><td>Defines if lazy loading mode is enabled (load children on expand via onLazyLoad event)</td>
</tr>
<tr>
<td>lazyLoadOnInit</td><td>Defines if lazy loading is triggered on initialization (only when lazy=true)</td>
</tr>
<tr>
<td>loading</td><td>Displays a loader to indicate data load in progress</td>
</tr>
<tr>
<td>loadingIcon</td><td>Icon shown on the loading mask when the table is in loading state</td>
</tr>
<tr>
<td>metaKeySelection</td><td>Whether metaKey (ctrl/cmd) should be considered for selection. Default false</td>
</tr>
<tr>
<td>multiSortMeta</td><td>Array of SortMeta objects to sort by default in multiple mode: [{field:'name', order:1}]</td>
</tr>
<tr>
<td>pageLinks</td><td>Number of page links to display in the paginator. Default 5</td>
</tr>
<tr>
<td>paginator</td><td>When true, enables pagination. Default false</td>
</tr>
<tr>
<td>paginatorLocale</td><td>Locale to be used in paginator formatting</td>
</tr>
<tr>
<td>paginatorPosition</td><td>Position of the paginator: 'top', 'bottom' or 'both'. Default 'bottom'</td>
</tr>
<tr>
<td>reorderableColumns</td><td>When enabled, columns can be reordered using drag and drop. Default false</td>
</tr>
<tr>
<td>resetPageOnSort</td><td>When true, resets the paginator to first page after sorting. Default true</td>
</tr>
<tr>
<td>resizableColumns</td><td>When enabled, columns can be resized using drag and drop. Default false</td>
</tr>
<tr>
<td>rowHover</td><td>Adds hover effect to rows. Default true</td>
</tr>
<tr>
<td>rows</td><td>Number of rows to display per page (paginator)</td>
</tr>
<tr>
<td>rowsPerPageOptions</td><td>Array of integer values for rows per page dropdown, e.g. [10,20,50]</td>
</tr>
<tr>
<td>rowTrackBy</td><td>Function to optimize dom operations by delegating to ngForTrackBy</td>
</tr>
<tr>
<td>scrollable</td><td>When specified, enables horizontal and/or vertical scrolling</td>
</tr>
<tr>
<td>scrollHeight</td><td>Height of the scroll viewport in fixed pixels or the 'flex' keyword for dynamic size</td>
</tr>
<tr>
<td>selection</td><td>Selected row(s): TreeTableNode in single mode, TreeTableNode[] in multiple mode</td>
</tr>
<tr>
<td>selectionMode</td><td>Selection mode: 'single', 'multiple' or null for no selection</td>
</tr>
<tr>
<td>showCurrentPageReport</td><td>Whether to display the current page report. Default false</td>
</tr>
<tr>
<td>showFirstLastIcon</td><td>When enabled, first and last page icons are displayed in paginator. Default true</td>
</tr>
<tr>
<td>showGridlines</td><td>Whether to show grid lines between cells. Default false</td>
</tr>
<tr>
<td>showJumpToPageDropdown</td><td>Whether to display a dropdown to navigate to any page. Default false</td>
</tr>
<tr>
<td>showLoader</td><td>Whether to show the loading mask when loading is true. Default true</td>
</tr>
<tr>
<td>showPageLinks</td><td>Whether to show page links in paginator. Default true</td>
</tr>
<tr>
<td>sortField</td><td>Name of the field to sort data by default</td>
</tr>
<tr>
<td>sortMode</td><td>Sorting mode: 'single' or 'multiple'. Default 'single'</td>
</tr>
<tr>
<td>sortOrder</td><td>Order to sort when default sorting is enabled: 1 ascending, -1 descending</td>
</tr>
<tr>
<td>styleClass</td><td>Style class of the component root element</td>
</tr>
<tr>
<td>tableStyle</td><td>Inline style object of the table, e.g. {width: '100%'}</td>
</tr>
<tr>
<td>tableStyleClass</td><td>Style class of the table element</td>
</tr>
<tr>
<td>totalRecords</td><td>Number of total records, defaults to length of value when not defined (lazy mode)</td>
</tr>
<tr>
<td>value</td><td>Array of TreeNode objects (hierarchical data). Node shape: {data: {...}, children: [...], expanded: bool}</td>
</tr>
<tr>
<td>virtualScroll</td><td>Whether the data should be loaded on demand during scroll. Default false</td>
</tr>
<tr>
<td>virtualScrollDelay</td><td>Delay in ms before triggering the virtual scroll. Default 150</td>
</tr>
<tr>
<td>virtualScrollItemSize</td><td>Height of a row to use in calculations of virtual scrolling. Default 28</td>
</tr>
</table>

**events**

<table>
<tr>
<th>name</th><th>comment</th>
</tr>
<tr>
<td>ActionClick</td><td>Fired when a button of an actions column is clicked. Data: {action: string (label or title), node: TreeNode, rowData: node.data}</td>
</tr>
<tr>
<td>ColReorder</td><td>Fired when a column is reordered. Data: {dragIndex, dropIndex, columns}</td>
</tr>
<tr>
<td>ColResize</td><td>Fired when a column is resized. Data: {element, delta}</td>
</tr>
<tr>
<td>ContextMenuSelect</td><td>Fired when a node is selected with right click. Data: {originalEvent, node}</td>
</tr>
<tr>
<td>EditCancel</td><td>Fired when cell edit is cancelled with escape key. Data: {field, data}</td>
</tr>
<tr>
<td>EditComplete</td><td>Fired when cell edit is completed. Data: {field, data}</td>
</tr>
<tr>
<td>EditInit</td><td>Fired when a cell switches to edit mode. Data: {field, data}</td>
</tr>
<tr>
<td>Filter</td><td>Fired when data is filtered. Data: {filters, filteredValue}</td>
</tr>
<tr>
<td>HeaderCheckboxToggle</td><td>Fired when state of header checkbox changes. Data: {originalEvent, checked}</td>
</tr>
<tr>
<td>LazyLoad</td><td>Fired when paging, sorting or filtering happens in lazy mode. Data: TreeTableLazyLoadEvent</td>
</tr>
<tr>
<td>NodeCollapse</td><td>Fired when a node is collapsed. Data: {originalEvent, node}</td>
</tr>
<tr>
<td>NodeExpand</td><td>Fired when a node is expanded. Data: {originalEvent, node}</td>
</tr>
<tr>
<td>NodeSelect</td><td>Fired when a node is selected (row click in selectionMode). Data: TreeTableNode</td>
</tr>
<tr>
<td>NodeUnselect</td><td>Fired when a node is unselected. Data: {originalEvent, node, type}</td>
</tr>
<tr>
<td>Page</td><td>Fired when pagination occurs. Data: TreeTablePaginatorState {page, first, rows, pageCount}</td>
</tr>
<tr>
<td>SelectionChange</td><td>Fired on selection change. Data: selected node(s)</td>
</tr>
<tr>
<td>Sort</td><td>Fired when a column gets sorted. Data: sort event {data, mode, field, order, multiSortMeta}</td>
</tr>
<tr>
<td>SortFunction</td><td>Custom sort event when customSort=true. Implement custom sorting in the page handler</td>
</tr>
</table>



