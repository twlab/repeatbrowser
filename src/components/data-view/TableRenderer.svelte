<script>
  import { beforeUpdate, createEventDispatcher } from "svelte";
  import { PivotData } from "./Utilities";
  import { spanSize } from "./helper";
  import { formatCellInfo, getFiltered } from './data-helper';
  const dispatch = createEventDispatcher();
  export let data;

  let pivotData = new PivotData(data);
  let colAttrs = pivotData.props.cols;
  let rowAttrs = pivotData.props.rows;
  let rowKeys = pivotData.getRowKeys();
  let colKeys = pivotData.getColKeys();

  beforeUpdate(() => {
    pivotData = new PivotData(data);
    colAttrs = pivotData.props.cols;
    rowAttrs = pivotData.props.rows;
    rowKeys = pivotData.getRowKeys();
    colKeys = pivotData.getColKeys();
  });

  function handleCellClick(input) {
      let filteredResults = getFiltered(data.data, input);
      dispatch('cell-click', filteredResults); 
  }

  
</script>

<table class="pvtTable">
  <thead>
    {#each colAttrs as c, j}
      <tr key={`colAttr${j}`}>
        {#if j === 0 && rowAttrs.length !== 0}
          <th colSpan={rowAttrs.length} rowSpan={colAttrs.length} />
        {/if}
        <th class="pvtAxisLabel">{c}</th>
        {#each colKeys as colKey, i}
          {#if spanSize(colKeys, i, j) !== -1}
            <th
              class="pvtColLabel"
              key={`colKey${i}`}
              colSpan={spanSize(colKeys, i, j)}
              rowSpan={j === colAttrs.length - 1 && rowAttrs.length !== 0 ? 2 : 1}>
              {colKey[j]}
              <!-- {#if colAttrs.length > 1 && colAttrs.length - 1 === j}
                <div class="rotate text-xs">{colKey[j]}</div>
              {:else}{colKey[j]}{/if} -->
            </th>
          {/if}
        {/each}
      </tr>
    {/each}
    <tr>
      {#each rowAttrs as r, i}
        <th class="pvtAxisLabel" key={`rowAttr${i}`}>{r}</th>
      {/each}
    </tr>

  </thead>

  <tbody>

    {#each rowKeys as rowKey, i}
      <tr key={`rowKeyRow${i}`}>

        {#each rowKey as txt, j}
          {#if spanSize(rowKeys, i, j) !== -1}
            <th
              key={`rowKeyLabel${i}-${j}`}
              class="pvtRowLabel"
              rowSpan={spanSize(rowKeys, i, j)}
              colSpan={j === rowAttrs.length - 1 && colAttrs.length !== 0 ? 2 : 1}>
              {#if rowAttrs.length !== 1 && spanSize(rowKeys, i, j) > 3}
                <div class="rotate90">{txt}</div>
              {:else}{txt}{/if}
            </th>
          {/if}
        {/each}
        {#each colKeys as colKey, j}
          <td
            class={`pvtVal pvtValue`}
            key={`pvtVal${i}-${j}`}
            on:click={() => handleCellClick(formatCellInfo(rowAttrs, rowKey, colAttrs, colKey, pivotData
                    .getAggregator(rowKey, colKey)
                    .value()))}>
            {pivotData
              .getAggregator(rowKey, colKey)
              .value() ? pivotData.getAggregator(rowKey, colKey).value() : ''}
          </td>
        {/each}
      </tr>
    {/each}
  </tbody>
</table>
