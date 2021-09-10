<script>
    import {
        Button,
        Form,
        FormGroup,
        FormText,
        Input,
        Label,
        Col,
        Container,
        Row,
    } from "sveltestrap";
    import Chart from "chart.js/auto";
    import MultiSelect from "./MultiSelect.svelte";

    const endpoint = "https://co2api.endpoint.ml/api/v1";

    let regions = [];
    let dataTypes = [];

    let region = [];
    let year = "";
    let dataType = "";

    import { onMount } from "svelte";

    let ready = false;
    onMount(() => {
        let ctx = document.getElementById("chart");
        chart = new Chart(ctx, {
            type: "pie",
            data: {},
        });
    });

    let chart;

    async function fetchObject(path) {
        const res = await fetch(endpoint + path);

        const json = await res.json();

        return json;
    }

    async function fetchRegions() {
        const json = await fetchObject("/distribution/regions");

        regions = json.results;

        return regions;
    }

    async function fetchDataTypes() {
        const json = await fetchObject("/distribution/dataTypes");

        dataTypes = json.results;

        console.log(dataTypes);

        return dataTypes;
    }

    function clearForm() {
        console.log("Clearing form!");
        year = "";
        dataType = "";
    }

    async function submit() {
        console.log("Gettting data...");
        console.log(region);
        console.log(year);
        console.log(dataType);
        console.log(regions);
        console.log(dataTypes);

        if (region == "" || year == "" || dataType == "") {
            alert("Invalid data!");
            return;
        }

        let res = {};
        for (let id of region) {
            const json = await fetchObject(
                `/distribution/summary?year=${year}&region=${parseInt(
                    id
                )}&dataType=${dataType}`
            );

            console.log(json.results);

            if (Object.keys(res).length > 4) {
                id = 0;
            }
            json.results.forEach((el, i) => {
                console.log(el.value);
                if (id in res) {
                    res[id] += el.value;
                } else {
                    res[id] = el.value;
                }
            });
        }

        console.log(res);

        chart.data.labels = [];
        Object.keys(res).forEach((a) => {
            for (let i of regions) {
                a = parseInt(a);
                if (a == 0) {
                    if (!chart.data.labels.includes("Other")) {
                        chart.data.labels.push("Other");
                    }
                }
                if (i.id == a) {
                    chart.data.labels.push(i.name);
                }
            }
        });
        console.log(chart.data.labels);

        chart.data.datasets = [
            {
                label: dataTypes.filter((a) => a.id == dataType)[0].name,
                data: Object.values(res),
                backgroundColor: [
                    "rgba(255, 99, 132, 0.2)",
                    "rgba(54, 162, 235, 0.2)",
                    "rgba(255, 206, 86, 0.2)",
                    "rgba(75, 192, 192, 0.2)",
                    "rgba(153, 102, 255, 0.2)",
                    "rgba(255, 159, 64, 0.2)",
                ],
                hoverOffset: 4,
            },
        ];
        chart.update();
    }
</script>

<Container>
    <Row>
        <Col xs="3">
            <Form>
                <FormGroup>
                    {#await fetchRegions()}
                        <p>Loading regions...</p>
                    {:then regions}
                        <Label for="region">Regions</Label>
                        <MultiSelect bind:value={region} id="regionSelect">
                            {#each regions as { name, id }}
                                <option value={id}>{name}</option>
                            {/each}
                        </MultiSelect>
                    {:catch error}
                        <p>{error.message}</p>
                    {/await}
                </FormGroup>
                <FormGroup>
                    <Label for="year">Year</Label>
                    <Input
                        bind:value={year}
                        type="select"
                        name="select"
                        id="yearSelect"
                    >
                        <option>2015</option>
                        <option>2016</option>
                        <option>2017</option>
                        <option>2018</option>
                        <option>2019</option>
                    </Input>
                </FormGroup>
                <FormGroup>
                    {#await fetchDataTypes()}
                        <p>Loading data types...</p>
                    {:then dataTypes}
                        <Label for="dataType">Data type</Label>
                        {#each dataTypes as { name, id }}
                            <Input
                                id={"r" + id}
                                type="radio"
                                bind:group={dataType}
                                value={id}
                                label={name}
                            />
                        {/each}
                    {:catch error}
                        <p>{error.message}</p>
                    {/await}
                </FormGroup>
            </Form>
            <Button color="danger" on:click={clearForm}>Clear form</Button>
            <Button color="success" on:click={submit}>Submit</Button>
        </Col>
        <Col>
            <canvas id="chart" />
        </Col>
    </Row>
</Container>
