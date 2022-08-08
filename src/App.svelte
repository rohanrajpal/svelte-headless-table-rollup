<script lang="ts">
  import { readable } from "svelte/store";
  import { createTable, Render, Subscribe } from "svelte-headless-table";
  import { addSortBy } from "svelte-headless-table/plugins";
  // Go to node_modules/svelte-headless-table/plugins/addSortBy.js
  // After line 131, add the following:
  /**
    console.log('toggling');
    console.log(cell);
    console.log(DataHeaderCell);
    console.log(cell instanceof DataHeaderCell);
   */
  // With `yarn dev`, cell instanceof DataHeaderCell returns false.
  // With `yarn build && yarn preview`, cell instanceof DataHeaderCEll returns true.

  const data = readable([
    { id: 0, name: "Bran" },
    { id: 1, name: "Ada" },
  ]);

  const table = createTable(data, {
    sort: addSortBy(),
  });

  const columns = table.createColumns([
    table.column({
      accessor: "id",
      header: "ID",
    }),
    table.column({
      accessor: "name",
      header: "Name",
    }),
  ]);

  const { headerRows, rows } = table.createViewModel(columns);
</script>

<table>
  <thead>
    {#each $headerRows as row}
      <Subscribe attrs={row.attrs()} let:attrs>
        <tr {...attrs}>
          {#each row.cells as cell}
            <Subscribe
              attrs={row.attrs()}
              let:attrs
              props={cell.props()}
              let:props
            >
              <th {...attrs} on:click={props.sort.toggle}>
                <Render of={cell.render()} />
              </th>
            </Subscribe>
          {/each}
        </tr>
      </Subscribe>
    {/each}
  </thead>
  <tbody>
    {#each $rows as row}
      <Subscribe attrs={row.attrs()} let:attrs>
        <tr {...attrs}>
          {#each row.cells as cell}
            <Subscribe attrs={row.attrs()} let:attrs>
              <td {...attrs}>
                <Render of={cell.render()} />
              </td>
            </Subscribe>
          {/each}
        </tr>
      </Subscribe>
    {/each}
  </tbody>
</table>
