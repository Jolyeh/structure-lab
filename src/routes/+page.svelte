<script>
  import { 
    Menu, FileSpreadsheet, Book, Grid3X3, Network, Palette,
    Search, Edit3, Database, EyeOff, FileText, X, Command
  } from 'lucide-svelte';

  let csvData = null;
  let fileName = "";
  let searchQuery = "";

  // Gestion des thèmes
  let currentTheme = "light";
  const themes = [
    { id: "light", name: "Light", colors: ["#570df8", "#f000b8", "#37cdbe", "#3d4451"] },
    { id: "dark", name: "Dark", colors: ["#661ae6", "#d926aa", "#1fb2a6", "#191d24"] },
    { id: "night", name: "Night", colors: ["#38bdf8", "#818cf8", "#f471b5", "#1e293b"] },
    { id: "cmyk", name: "CMYK", colors: ["#45afe5", "#e64980", "#ffed00", "#1a1a1a"] }
  ];

  const setTheme = (theme) => {
    currentTheme = theme;
    document.documentElement.setAttribute('data-theme', theme);
  };

  const handleFileUpload = (event) => {
    const file = event.target.files[0];
    if (!file) return;
    fileName = file.name;
    const reader = new FileReader();
    reader.onload = (e) => {
      const text = e.target.result;
      const lines = text.split('\n');
      const headers = lines[0].split(',');
      csvData = lines.slice(1).filter(line => line.trim() !== "").map(line => {
        const values = line.split(',');
        return headers.reduce((obj, header, i) => {
          obj[header.trim()] = values[i]?.trim();
          return obj;
        }, {});
      });
    };
    reader.readAsText(file);
  };

  const resetFile = () => { csvData = null; fileName = ""; searchQuery = ""; };
  const openModal = (id) => document.getElementById(id).showModal();
  const closeModal = (id) => document.getElementById(id).close();

  const selectSearchResult = (value) => {
    searchQuery = value;
    closeModal('modal_search');
  };

  $: searchResults = csvData && searchQuery 
    ? csvData.filter(row => Object.values(row).join(' ').toLowerCase().includes(searchQuery.toLowerCase())).slice(0, 8)
    : [];
</script>

<dialog id="modal_dict" class="modal">
  <div class="modal-box max-w-5xl h-[70vh] border border-base-300 shadow-2xl">
    <div class="flex items-center justify-between mb-6 text-primary">
      <h3 class="font-black text-2xl uppercase tracking-widest flex items-center gap-3">
        <Book size={28} /> Dictionnaire
      </h3>
      <form method="dialog"><button class="btn btn-sm btn-circle btn-ghost"><X /></button></form>
    </div>
    <div class="p-10 bg-base-200/30 rounded-3xl border border-dashed border-base-300 h-full flex items-center justify-center opacity-30">
        Indexation du dictionnaire de données...
    </div>
  </div>
  <form method="dialog" class="modal-backdrop backdrop-blur-md bg-base-900/40"><button>close</button></form>
</dialog>

<dialog id="modal_matrix" class="modal">
  <div class="modal-box max-w-5xl h-[70vh] border border-base-300 shadow-2xl">
    <div class="flex items-center justify-between mb-6 text-primary">
      <h3 class="font-black text-2xl uppercase tracking-widest flex items-center gap-3">
        <Grid3X3 size={28} /> Matrice
      </h3>
      <form method="dialog"><button class="btn btn-sm btn-circle btn-ghost"><X /></button></form>
    </div>
    <div class="p-10 bg-base-200/30 rounded-3xl border border-dashed border-base-300 h-full flex items-center justify-center opacity-30">
        Génération de la matrice de connectivité...
    </div>
  </div>
  <form method="dialog" class="modal-backdrop backdrop-blur-md bg-base-900/40"><button>close</button></form>
</dialog>

<dialog id="modal_graph" class="modal">
  <div class="modal-box max-w-5xl h-[70vh] border border-base-300 shadow-2xl">
    <div class="flex items-center justify-between mb-6 text-primary">
      <h3 class="font-black text-2xl uppercase tracking-widest flex items-center gap-3">
        <Network size={28} /> Visualisation Graphe
      </h3>
      <form method="dialog"><button class="btn btn-sm btn-circle btn-ghost"><X /></button></form>
    </div>
    <div class="p-10 bg-base-200/30 rounded-3xl border border-dashed border-base-300 h-full flex items-center justify-center">
        <div class="text-center">
            <Network size={48} class="mx-auto mb-4 opacity-10 animate-pulse" />
            <p class="opacity-30 uppercase tracking-widest font-bold">Moteur de rendu des relations...</p>
        </div>
    </div>
  </div>
  <form method="dialog" class="modal-backdrop backdrop-blur-md bg-base-900/40"><button>close</button></form>
</dialog>

<dialog id="modal_search" class="modal">
  <div class="modal-box max-w-3xl p-0 overflow-hidden border border-base-300 shadow-2xl bg-base-100">
    <div class="p-6 bg-base-200/50 border-b border-base-300 flex items-center gap-4">
      <Search size={24} class="text-primary" />
      <input type="text" bind:value={searchQuery} placeholder="Rechercher..." class="bg-transparent outline-none w-full text-xl font-medium" autofocus />
    </div>
    <div class="p-3 max-h-[50vh] overflow-y-auto space-y-1">
      {#each searchResults as res}
        <button on:click={() => selectSearchResult(Object.values(res)[0])} class="w-full text-left p-4 hover:bg-primary hover:text-primary-content rounded-2xl transition-all group flex items-center justify-between">
          <div class="flex flex-col">
            <span class="font-bold text-lg">{Object.values(res)[0]}</span>
            <span class="text-xs opacity-50 group-hover:text-white/70 truncate">{Object.values(res).slice(1, 4).join(' • ')}</span>
          </div>
          <Command size={18} class="opacity-0 group-hover:opacity-100" />
        </button>
      {:else}
        <div class="p-12 text-center opacity-30 italic">Aucun résultat pour "{searchQuery}"</div>
      {/each}
    </div>
  </div>
  <form method="dialog" class="modal-backdrop backdrop-blur-md bg-base-900/60"><button>close</button></form>
</dialog>

<div class="drawer lg:drawer-open font-sans antialiased text-base-content">
  <input id="my-drawer" type="checkbox" class="drawer-toggle" />

  <div class="drawer-content flex flex-col bg-base-50/30">
    <div class="navbar w-full bg-base-100 border-b border-base-200 lg:hidden px-4">
      <div class="flex-none">
        <label for="my-drawer" class="btn btn-square btn-ghost"><Menu size={24} /></label>
      </div>
      <div class="flex-1 px-2 font-bold tracking-tight text-xl italic text-primary uppercase">Structure-Lab</div>
    </div>

    <main class="p-6 md:p-10 space-y-8 max-w-6xl mx-auto w-full">
      <header class="flex flex-col gap-2 text-center lg:text-left">
        <h1 class="text-2xl font-extrabold tracking-tight lg:text-4xl">Analyseur de Structure</h1>
        <p class="text-base-content/60 max-w-lg mx-auto lg:mx-0">Importez le fichier source (.csv) pour commencer l'analyse.</p>
      </header>

      <section class="max-w-md mx-auto lg:mx-0">
        {#if !csvData}
          <input type="file" accept=".csv" on:change={handleFileUpload} class="file-input file-input-bordered file-input-primary w-full shadow-lg" />
        {:else}
          <div class="flex items-center gap-3 bg-base-100 p-4 rounded-2xl border border-base-200 shadow-sm animate-in zoom-in">
            <FileText class="text-primary" size={20} />
            <span class="flex-1 font-bold text-sm truncate">{fileName}</span>
            <button on:click={resetFile} class="btn btn-circle btn-ghost btn-xs"><X size={16} /></button>
          </div>
        {/if}
      </section>

      <div class="relative group">
        <div class="absolute -inset-1 bg-gradient-to-br from-primary/10 via-transparent to-secondary/10 rounded-2xl blur-lg opacity-50"></div>
        <div class="relative h-[55vh] w-full bg-base-100 rounded-2xl border border-base-300 shadow-2xl overflow-hidden">
          {#if csvData}
            <div class="overflow-auto h-full p-4 animate-in fade-in duration-500">
              <table class="table table-zebra table-pin-rows">
                <thead>
                  <tr class="bg-base-100">
                    {#each Object.keys(csvData[0]) as header}
                      <th class="text-primary uppercase text-[10px] tracking-widest">{header}</th>
                    {/each}
                  </tr>
                </thead>
                <tbody>
                  {#each csvData.filter(row => Object.values(row).join(' ').toLowerCase().includes(searchQuery.toLowerCase())) as row}
                    <tr>
                      {#each Object.values(row) as value}
                        <td class="text-sm opacity-80">{value}</td>
                      {/each}
                    </tr>
                  {/each}
                </tbody>
              </table>
            </div>
          {:else}
            <div class="flex flex-col items-center justify-center h-full text-center space-y-4 opacity-20">
              <EyeOff size={48} strokeWidth={1.5} />
              <p class="text-sm font-bold tracking-widest uppercase">En attente de données</p>
            </div>
          {/if}
        </div>
      </div>

      <div class="flex flex-wrap items-center justify-between gap-4 pt-6 border-t border-base-200">
        <button class="btn btn-primary btn-wide transition-all hover:scale-[1.02]" disabled={!csvData}>
          <Edit3 size={18} class="mr-2" /> Éditer la Structure
        </button>
        
        <div class="flex items-center gap-3 bg-base-200/50 px-4 py-2 rounded-full border border-base-300">
          <span class="relative flex h-2 w-2">
            <span class="animate-ping absolute inline-flex h-full w-full rounded-full bg-success opacity-75"></span>
            <span class="relative inline-flex rounded-full h-2 w-2 bg-success"></span>
          </span>
          <span class="text-xs font-bold font-mono tracking-tighter uppercase opacity-70">
            Status: <span class="text-primary">{csvData ? 'Données actives' : 'Standby'}</span>
          </span>
        </div>
      </div>
    </main>
  </div>

  <div class="drawer-side border-r border-base-200/50 shadow-2xl z-50">
    <label for="my-drawer" aria-label="close sidebar" class="drawer-overlay"></label>
    <aside class="menu p-6 w-80 min-h-full bg-base-100 text-base-content flex flex-col">
      <div class="flex items-center gap-4 px-2 mb-12">
        <div class="bg-primary text-primary-content p-2.5 rounded-xl shadow-inner rotate-3 transition-transform hover:rotate-0">
          <Database size={24} strokeWidth={2.5} />
        </div>
        <div class="flex flex-col font-black tracking-tighter leading-none">
          <span>STRUCTURE</span>
          <span class="text-[10px] text-primary tracking-[0.2em]">LAB V2.0</span>
        </div>
      </div>

      <nav class="space-y-8 flex-1">
        <div>
          <p class="px-4 text-[11px] font-bold text-base-content/30 uppercase tracking-[0.15em] mb-4 text-primary">Analyse</p>
          <ul class="space-y-2">
            <li><button on:click={() => openModal('modal_dict')} class="flex w-full gap-4 py-3 rounded-xl hover:bg-base-200 transition-all font-medium"><Book size={20} />Dictionnaire</button></li>
            <li><button on:click={() => openModal('modal_matrix')} class="flex w-full gap-4 py-3 rounded-xl hover:bg-base-200 transition-all font-medium"><Grid3X3 size={20} />Matrice</button></li>
            <li><button on:click={() => openModal('modal_graph')} class="flex w-full gap-4 py-3 rounded-xl hover:bg-base-200 transition-all font-medium"><Network size={20} />Graphe</button></li>
          </ul>
        </div>

        <div>
          <p class="px-4 text-[11px] font-bold text-base-content/30 uppercase tracking-[0.15em] mb-4 text-primary">Outils</p>
          <ul class="space-y-2">
            <li>
              <button on:click={() => openModal('modal_search')} class="flex w-full gap-4 py-3 rounded-xl bg-primary/10 text-primary hover:bg-primary hover:text-primary-content transition-all font-bold">
                <Search size={20} /> Recherche
              </button>
            </li>
          </ul>
        </div>
      </nav>

      <div class="mt-auto pt-6 border-t border-base-200">
        <div class="flex items-center gap-2 px-4 mb-4 text-primary">
          <Palette size={16} />
          <p class="text-[11px] font-bold uppercase tracking-[0.15em]">Thèmes</p>
        </div>
        <div class="grid grid-cols-2 gap-2">
          {#each themes as theme}
            <button on:click={() => setTheme(theme.id)} class="flex flex-col gap-2 p-3 rounded-xl border transition-all {currentTheme === theme.id ? 'border-primary bg-primary/5' : 'border-base-300'}">
              <span class="text-[10px] font-black uppercase truncate">{theme.name}</span>
              <div class="flex gap-0.5 h-2 w-full rounded-full overflow-hidden">
                {#each theme.colors as color} <div class="flex-1" style="background: {color}"></div> {/each}
              </div>
            </button>
          {/each}
        </div>
      </div>
    </aside>
  </div>
</div>