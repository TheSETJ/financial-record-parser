<script lang="ts">
    let inputValue: string = '';
    let parsedValue: string = '';

    function parse(record: string): string {
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