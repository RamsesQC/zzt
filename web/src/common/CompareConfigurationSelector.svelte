<script lang="ts">
  import {
    backend,
    comparisonConfiguration,
    currentProjectID,
    mutationCounter,
    projectStorage,
  } from "../stores";

  let savedConfigurations = $derived(
    $currentProjectID
      ? $projectStorage.savedConfigurations($currentProjectID)
      : [],
  );
  let selectedConfiguration = $derived(
    $currentProjectID &&
      $comparisonConfiguration?.projectID == $currentProjectID
      ? savedConfigurations.find(
          (configuration) =>
            configuration.name == $comparisonConfiguration?.name,
        )
      : undefined,
  );

  $effect(() => {
    try {
      $backend!.setComparisonConfiguration(
        selectedConfiguration?.snapshot ?? null,
      );
      mutationCounter.update((count) => count + 1);
    } catch (err) {
      window.alert(`Couldn't use comparison configuration: ${err}`);
      comparisonConfiguration.set(undefined);
    }
  });

  function changeSelection(event: Event) {
    const name = (event.currentTarget as HTMLSelectElement).value;
    if (!$currentProjectID || !name) {
      comparisonConfiguration.set(undefined);
      return;
    }
    comparisonConfiguration.set({ projectID: $currentProjectID, name });
  }
</script>

<label>
  Compare active configuration against:
  <select value={selectedConfiguration?.name ?? ""} onchange={changeSelection}>
    <option value="">Original OSM baseline</option>
    {#each savedConfigurations as configuration (configuration.name)}
      <option value={configuration.name}>{configuration.name}</option>
    {/each}
  </select>
</label>
