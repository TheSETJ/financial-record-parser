<script lang="ts">
  let inputValue: string = '';
  let parsedValues: Array<string> = [];

  function parse(record: string): string {
    const verb = parseVerb(record);
    const amount = parseAmount(record);
    const explanation = parseExplanation(record, verb, amount);

    return `${verb},${amount},"${explanation}"`;
  }

  function parseVerb(record: string): string {
    const verbs = {
      پرداخت: 'paid',
      دریافت: 'received',
      جابه‌جایی: 'transferred'
    };

    for (const [persianVerb, englishVerb] of Object.entries(verbs)) {
      if (record.startsWith(persianVerb)) {
        return englishVerb;
      }
    }

    return 'paid';
  }

  function parseAmount(record: string): string {
    record = record.replace(/[۰-۹]/g, (digit) =>
      String.fromCharCode(digit.charCodeAt(0) - 1728)
    );

    const parts = record.trim().split(/\s+/);
    let totalAmount = 0;

    for (let i = 1; i < parts.length; i++) {
      const part = parts[i];

      if (part === 'و') {
        continue;
      }

      if (part === 'ریال') {
        break;
      }

      if (part === 'تومان') {
        totalAmount *= 10;

        continue;
      }

      if (part.includes('میلیارد')) {
        const numericPart = parseInt(part.replace('میلیارد', '').trim(), 10);

        if (!isNaN(numericPart)) {
          totalAmount += numericPart * 1e9;
        }

        continue;
      }

      if (part.includes('میلیون')) {
        const numericPart = parseInt(part.replace('میلیون', '').trim(), 10);

        if (!isNaN(numericPart)) {
          totalAmount += numericPart * 1e6;
        }

        continue;
      }

      if (part.includes('هزار')) {
        const numericPart = parseInt(part.replace('هزار', '').trim(), 10);

        if (!isNaN(numericPart)) {
          totalAmount += numericPart * 1e3;
        }

        continue;
      }

      const numericPart = parseInt(part, 10);

      if (!isNaN(numericPart)) {
        totalAmount += numericPart;
      } else {
        break;
      }
    }

    return totalAmount.toString();
  }

  function parseExplanation(record: string, verb: string, amount: string): string {
    record = record.replace(/و\s*[۰-۹]+\s*ریال/, '').trim();

    const tomanIndex = record.indexOf('تومان');

    if (tomanIndex !== -1) {
      return record.substring(tomanIndex + 6).trim();
    }

    return record;
  }

  function parseLines() {
    parsedValues = inputValue.split('\n').map((record) => parse(record));
  }
</script>

<div class="container">
  <textarea rows="5" bind:value={inputValue} on:input={parseLines}></textarea>
  <output class="paragraphs">
    {#each parsedValues as parsedValue, index (index)}
      <p>{parsedValue}</p>
    {/each}
  </output>
</div>

<style>
  @import '../../public/build/tailwind.css';
</style>
