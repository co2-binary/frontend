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

    const endpoint = "https://co2ding-2021.herokuapp.com/api/v1";

    let regions = [];
    let dataTypes = [];

    let region = "";
    let year = "";
    let dataType = "";

    import { onMount } from "svelte";

    let ready = false;
    onMount(() => {
        let ctx = document.getElementById("chart");
        chart = new Chart(ctx, {
            type: "bar",
            data: {},
            options: {
                scales: {
                    y: {
                        beginAtZero: true,
                    },
                },
            },
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
        region = "";
        year = "";
        dataType = "";
    }

    async function submit() {
        console.log("Gettting data...");
        console.log(region);
        console.log(year);
        console.log(dataType);

        if (region == "" || year == "" || dataType == "") {
            alert("Invalid data!");
            return;
        }

        const json = await fetchObject(
            `/distribution/summary?year=${year}&region=${region}&dataType=${dataType}`
        );

        let res = json.results;

        console.log(res);

        chart.data.labels = res.map((a) => a.dateStart);
        chart.data.datasets = [
            {
                label: dataTypes.filter((a) => a.id == dataType)[0].name,
                data: res.map((a) => a.value),
                backgroundColor: [
                    "rgba(255, 99, 132, 0.2)",
                    "rgba(54, 162, 235, 0.2)",
                    "rgba(255, 206, 86, 0.2)",
                    "rgba(75, 192, 192, 0.2)",
                    "rgba(153, 102, 255, 0.2)",
                    "rgba(255, 159, 64, 0.2)",
                ],
                borderColor: [
                    "rgba(255, 99, 132, 1)",
                    "rgba(54, 162, 235, 1)",
                    "rgba(255, 206, 86, 1)",
                    "rgba(75, 192, 192, 1)",
                    "rgba(153, 102, 255, 1)",
                    "rgba(255, 159, 64, 1)",
                ],
                borderWidth: 1,
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
                        <Label for="region">Region</Label>
                        <Input
                            bind:value={region}
                            type="select"
                            name="select"
                            id="regionSelect"
                        >
                            {#each regions as { name, id }}
                                <option value={id}>{name}</option>
                            {/each}
                        </Input>
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
