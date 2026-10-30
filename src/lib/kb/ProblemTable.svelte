<script lang="ts">
  import reports from '#lib/data/reports-4471.json';
  import notes from '#lib/data/desk-notes.json';
  import { groupByCause, uniqueById, type Report } from '#lib/kb/causes';
  import { tenantOf } from '#lib/kb/locales';

  let { ticket }: { ticket: number } = $props();

  const TITLES: Record<string, string> = {
    parser: 'the parser reads dates month first',
    hint: 'booking-v3 prints MM/DD/YYYY under the date field',
    moved: 'dates arrive one day off',
  };

  const merged = $derived(uniqueById(reports as Report[]).filter((r) => r.mergedInto === ticket));
  // desk-notes.json: reports the desk read as a one-day move. The month is right and the day is not,
  // so they are a problem of their own, not part of the month flip they are filed under.
  const moved = $derived(merged.filter((r) => notes.oneDayMove.includes(r.id)));
  const flipped = $derived(merged.filter((r) => !notes.oneDayMove.includes(r.id)));
  const problems = $derived([
    ...groupByCause(flipped, tenantOf).map((g) => ({ key: g.cause as string, g })),
    ...groupByCause(moved, tenantOf).map((g) => ({ key: 'moved', g })),
  ]);
  const tags = (g: { taggedAs: Record<string, number> }) =>
    Object.entries(g.taggedAs)
      .sort((a, b) => b[1] - a[1])
      .map(([tag, n]) => `${tag} ×${n}`)
      .join(' · ');
</script>

<p class="sum">
  <strong>{merged.length}</strong> reports merged into #{ticket} ·
  <strong>{problems.length}</strong> problems
</p>

<table>
  <thead>
    <tr><th>problem</th><th>reports</th><th>tagged as</th><th>tenants</th><th>seen</th></tr>
  </thead>
  <tbody>
    {#each problems as { key, g } (key)}
      <tr>
        <td>{TITLES[key] ?? key}</td>
        <td>{g.reports.length}</td>
        <td class="tags">{tags(g)}</td>
        <td>{g.tenants}</td>
        <td>{g.firstSeen} → {g.lastSeen}</td>
      </tr>
    {/each}
  </tbody>
</table>

<style>
  .sum { font-size: 1.1rem; }
  .tags { color: var(--muted); font-size: 0.85em; }
</style>
