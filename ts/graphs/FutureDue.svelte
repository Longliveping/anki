<script lang="typescript">
    import type { I18n } from "anki/i18n";
    import type { HistogramData } from "./histogram-graph";
    import { GraphRange, RevlogRange } from "./graph-helpers";
    import type { TableDatum } from "./graph-helpers";
    import { gatherData, buildHistogram } from "./future-due";
    import type { GraphData } from "./future-due";
    import type pb from "anki/backend_proto";
    import HistogramGraph from "./HistogramGraph.svelte";
    import GraphRangeRadios from "./GraphRangeRadios.svelte";
    import TableData from "./TableData.svelte";

    export let sourceData: pb.BackendProto.GraphsOut | null = null;
    export let i18n: I18n;

    let graphData = null as GraphData | null;
    let histogramData = null as HistogramData | null;
    let tableData: TableDatum[] = [] as any;
    let backlog: boolean = true;
    let graphRange: GraphRange = GraphRange.Month;

    $: if (sourceData) {
        graphData = gatherData(sourceData);
    }

    $: if (graphData) {
        ({ histogramData, tableData } = buildHistogram(
            graphData,
            graphRange,
            backlog,
            i18n
        ));
    }

    const title = i18n.tr(i18n.TR.STATISTICS_FUTURE_DUE_TITLE);
    const subtitle = i18n.tr(i18n.TR.STATISTICS_FUTURE_DUE_SUBTITLE);
    const backlogLabel = i18n.tr(i18n.TR.STATISTICS_BACKLOG_CHECKBOX);
</script>

<div class="graph" id="graph-future-due">
    <h1>{title}</h1>

    <div class="subtitle">{subtitle}</div>

    <div class="range-box-inner">
        {#if graphData && graphData.haveBacklog}
            <label>
                <input type="checkbox" bind:checked={backlog} />
                {backlogLabel}
            </label>
        {/if}

        <GraphRangeRadios bind:graphRange {i18n} revlogRange={RevlogRange.All} />
    </div>

    <HistogramGraph data={histogramData} {i18n} />

    <TableData {i18n} {tableData} />
</div>
