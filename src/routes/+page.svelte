<script lang="ts">
    let inputValue: string = '';
    let parsedValue: string = '';

    function parse(record: string): string {
        record = parseVerb(record);

        return record;
    }

    function parseVerb(record: string): string {
        const verbs = ['پرداخت', 'دریافت', 'جابه‌جایی'];

        let hasVerb = verbs.some((verb) => record.startsWith(verb));

        if (!hasVerb) {
            record = verbs[0] + ' ' + record;
        }

        return record;
    }

    $: parsedValue = parse(inputValue);
</script>

<style>
    @import '../../public/build/tailwind.css';
</style>

<div class="container">
    <input type="text" bind:value={inputValue} />
    <p>{parsedValue}</p>
</div>