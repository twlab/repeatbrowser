<script>
  import { onMount } from 'svelte';
  import Tab, { Icon, Label } from "@smui/tab";
  import TabBar from "@smui/tab-bar";
  import Button from "@smui/button";
  import DataCenter from "./DataCenter.svelte";
  import IdeogramExample from "./examples/IdeogramExample.svelte";
  import PackChartRepeats from "./examples/PackChartRepeats.svelte";
  import PlotlyHeatmap from "./examples/PlotlyHeatmap.svelte";
  import PlotlyHeatmapContainer from "./examples/PlotlyHeatmapContainer.svelte";
  import PlotlyHeatmapRatio from "./examples/PlotlyHeatmapRatio.svelte";
  import PlotlyAreaChart from "./examples/PlotlyAreaChart.svelte";
  import ConsensusContainer from "./components/consensus/ConsensusContainer.svelte";
  import ConsensusRatioContainer from "./components/consensus/ConsensusRatioContainer.svelte";
  import GenomeViewContainer from "./components/genome-view/GenomeViewContainer.svelte";
  import GenomeViewRatioContainer from "./components/genome-view/GenomeViewRatioContainer.svelte";
  import PivotTable from './components/data-view/PivotTable.svelte';
  import LinearProgress from "./ui/LinearProgress.svelte";
  import Logo from "./ui/Logo.svelte";
  import CartComponent from "./components/Cart.svelte";
  import CartIndicator from './ui/CartIndicator.svelte';
  import Switch from './ui/Switch.svelte';
  import Modal from './ui/Modal.svelte';
  import _data from "./json/main.json";
  import defaultData from './json/default_cart_data.json';

  import { Cart } from "./stores/CartStore";
  let dataInStore;
  let repeatsInStore;
  let mode = 'files';

  const unsubscribe = Cart.subscribe(store => {
    const { data, repeats } = store;
    dataInStore = data;
    repeatsInStore = repeats;
    console.log(data);
  });

  onMount(() => {
    Cart.addDataItems(defaultData.data);
    Cart.addRepeats(defaultData.repeats);
  })

  let iconTabs = [
    {
      k: 0,
      icon: "databases",
      label: "Heatmap"
    },
    {
      k: 1,
      icon: "databases",
      label: "Data"
    },
    {
      k: 2,
      icon: "repeat",
      label: "Repeats"
    },
    {
      k: 3,
      icon: "shopping_cart",
      label: "Cart"
    },
    {
      k: 4,
      icon: "people",
      label: "Consensus View"
    },
    {
      k: 5,
      icon: "dna",
      label: "Genome View"
    }
  ];
  let keyedTabsActive = iconTabs[0];
  let combination = undefined;

  function handleHeatmapClick(event) {
    combination = event.detail;
    keyedTabsActive = iconTabs[4];
  }

  function handleModeChange(event) {
    if (event.detail) {
      mode = 'experiments';
      keyedTabsActive = iconTabs[1];
    } else {
      mode = 'files'
    }
    Cart.addDataItems([]);
  }
  
</script>

<style>
  section > div {
    margin-bottom: 40px;
  }

  .main-body {
    width: 80%;
    margin-left: 10%;
    margin-right: 10%;
    margin-top: 2rem;
  } 

  .heatmap-hide {
    display: none;
  }

  .svg-box {
    width: 100%;
    height: 10%;
    position: relative;
  }
</style>

<div>
  <div class="svg-box">
  <Logo />
  </div>

  <div class="flex justify-center">
    <Switch on:mode-change={handleModeChange}/>
  </div>

  <div class="border-t-2 border-blue-300 my-8"></div>

  <TabBar
    tabs={iconTabs}
    let:tab
    key={tab => tab.k}
    bind:active={keyedTabsActive}>
    <Tab
      {tab}
      stacked={true}
      indicatorSpanOnlyContent={true}
      tabIndicator$transition="fade">
      <Icon class="material-icons">{tab.icon}</Icon>
      <Label>
      {#if tab.k === 3}
            <CartIndicator {mode}/>
          {:else}{tab.label}{/if}
      </Label>
    </Tab>
  </TabBar>
  <div class="border-t-2 border-orange-300 py-2 my-2"></div>


  <div class="main-body">
    <!-- <ContactCard/> -->
    <!-- <DataCenter/> -->
    <!-- <ChartExample/> -->
    <!-- <AreaChartExample/>  -->
    <!-- <IdeogramExample/>  -->
    <!-- <PackChartExample/> -->
    <!-- <PlotlyHeatmapRatio/> -->
    <!-- <PlotlyAreaChart/> -->
    <!-- <ConsensusContainer/> -->
    <!-- <GenomeViewContainer/> -->
    <!-- <GenomeViewRatioContainer/> -->
    {#if keyedTabsActive.k === 1}
      <!-- <DataCenter DATA={_data[mode]}/> -->
      
      <Modal>
        <PivotTable DATA={_data[mode]}/>
      </Modal>
    {:else if keyedTabsActive.k === 2}
      <PackChartRepeats />
    {:else if keyedTabsActive.k === 3}
      <CartComponent />
    {:else if keyedTabsActive.k === 4}
      {#if combination !== undefined}
        {#if mode === 'experiments'}
           <ConsensusRatioContainer {combination} files={_data.files}/>
        {:else}
           <ConsensusContainer {combination} />
        {/if}
			{:else}
				<p>Please select data and repeats and click on heatmap cell</p>
      {/if}

    {:else if keyedTabsActive.k === 5}
      {#if combination !== undefined}
        {#if mode === 'experiments'}
           <GenomeViewRatioContainer {combination}/>
        {:else}
           <GenomeViewContainer {combination} />
        {/if}
			{:else}
				<p>Please select data and repeats and click on heatmap cell</p>
      {/if}
			
    {:else if keyedTabsActive.k === 0 && (dataInStore.length === 0 || repeatsInStore.length === 0)}
      <p>Please select data and repeats</p>
    {/if} 
  </div>
  <div class:heatmap-hide={keyedTabsActive.k !== 0}>
    {#if mode === 'experiments'}
      <PlotlyHeatmapRatio on:heatmap-click={handleHeatmapClick} files={_data.files} />    
    {:else}
      <PlotlyHeatmapContainer on:heatmap-click={handleHeatmapClick} />    
    {/if}
  </div>


</div>
