<script lang="ts">
    let inputValue: string = '';
    let parsedValue: string = '';

    function parse(record: string): string {
        record = parseVerb(record);

        record = replaceAmount(record);

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

    function replaceAmount(record: string): string {
        const amountRegex = /[۰-۹].*(:?هزار|میلیون|میلیارد|تومان|ریال|\sو\s|[۰-۹]{1,3})\s?/g;

        return record.replace(amountRegex, (match, ...groups) => {
            const numericValue = parseAmount(match);
            return numericValue.toString() + ' ریال ';
        });
    }

    function parseAmount(amountString: string): any {
        amountString = amountString.replace(/[۰-۹]/g, (digit) => String.fromCharCode(digit.charCodeAt(0) - 1728));

        const parts = amountString.split(/\s+/);

        const numericValues: number[] = [];

        let rialPartAlert = false;

        for (const part of parts) {
            if (part === 'ریال') {
                let numericPart = numericValues[numericValues.length - 1];
                if (numericPart > 10) {
                    numericValues[numericValues.length - 1] = numericPart / 10;
                }
                continue;
            }

            if (part === 'تومان') {
                rialPartAlert = true;
            }

            if (part === 'و') {
                continue;
            }

            if (part.includes('میلیارد')) {
                const numericPart = parseInt(part.replace('میلیارد', ''), 10);
                if (!isNaN(numericPart)) {
                    numericValues.push(numericPart * 1e10);
                }
                continue;
            }

            if (part.includes('میلیون')) {
                const numericPart = parseInt(part.replace('میلیون', ''), 10);
                if (!isNaN(numericPart)) {
                    numericValues.push(numericPart * 1e7);
                }
                continue;
            }

            if (part.includes('هزار')) {
                const numericPart = parseInt(part.replace('هزار', ''), 10);
                if (!isNaN(numericPart)) {
                    numericValues.push(numericPart * 1e4);
                }
                continue;
            }

            const numericPart = parseInt(part, 10);
            if (!isNaN(numericPart)) {
                if (rialPartAlert) {
                    numericValues.push(numericPart);
                } else {
                    numericValues.push(numericPart * 10)
                    rialPartAlert = true;
                }
            }
        }

        return numericValues.reduce((sum, value) => sum + value, 0);
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