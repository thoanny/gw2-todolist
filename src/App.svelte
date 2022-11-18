<script>
    import { onMount } from 'svelte';

    // @ts-ignore
    import { v4 as uuidv4 } from 'uuid';
    import { copy } from 'svelte-copy';
    import moment from 'moment'

    import SunIcon from './assets/sun.svg';
    import MoonIcon from './assets/moon.svg';

    let todolist = [];
    let _my = {
        "date": moment.utc().format('YYYY-MM-DD'),
        "tasks": []
    };

    let my;

    onMount(()=> {
        if(localStorage.getItem('todolist')) {
            todolist = JSON.parse(atob(localStorage.getItem('todolist')));
        }

        if(localStorage.getItem('my')) {
            my = JSON.parse(localStorage.getItem('my'));
        } else {
            my = _my;
        }

        if(my.date !== moment.utc().format('YYYY-MM-DD')) {
            my = _my;
        }
        updateMy();
    });

    function updateTodolist() {
        localStorage.setItem('todolist', btoa(JSON.stringify(todolist)));
    }

    function updateMy() {
        localStorage.setItem('my', JSON.stringify(my));
    }

    function updateMyTasks() {
        const id = this.dataset.taskId;
        const i = my.tasks.indexOf(id);
        if(this.checked && i < 0) {
            my.tasks = [...my.tasks, id];
        } else if(!this.checked && i >= 0) {
            my.tasks = [...my.tasks.slice(0, i), ...my.tasks.slice(i + 1)];
        }

        updateMy();
    }

    let addTaskOpened = false;

    function addTask(e) {
        const formData = new FormData(e.target);

        const data = {};
        data['id'] = uuidv4();
        for (let field of formData) {
            const [key, value] = field;
            data[key] = (value)?value:null;
        }

        // @ts-ignore
        todolist = [...todolist, data];
        updateTodolist();
        e.target.reset();

        addTaskOpened = false;
    }

    function removeTask() {
        const id = this.dataset.taskId;
        
        const i = my.tasks.indexOf(id);
        if(i >= 0) {
            my.tasks = [...my.tasks.slice(0, i), ...my.tasks.slice(i + 1)];
        }

        const j = todolist.findIndex(task => task.id === id);
        if(j >= 0) {
            todolist = [...todolist.slice(0, j), ...todolist.slice(j + 1)];
        }

        updateMy();
        updateTodolist();
    }

    function resetAddTask() {
        addTaskOpened = false;
    }

    function formatTime(time, moment) {
        if(!moment) {
            if(time) {
                return time;
            } else {
                return '&nbsp;';
            }
        } else {
            const min = time.split(':')[1];
            return `<span class="flex items-center"><img src="${(moment == 'day')?SunIcon:MoonIcon}" class="w-4 h-4 inline-block" />:${min}</span>`;
        }
    }

    // $: exportTasks = btoa(JSON.stringify(todolist));
    function exportTodolist(todolist) {
        if(todolist.length <= 0) {
            return '';
        }
        return btoa(JSON.stringify(todolist));
    }

    let importError = false;

    function importTodolist() {
        importError = false;

        if(this.value) {
            try {
                const newTodolist = JSON.parse(atob(this.value));
                todolist = newTodolist;
                updateTodolist();
            } catch(err) {
                importError = true;
            }
        }        
    }
</script>

<main>
    <div class="container mx-auto my-6 max-w-4xl">

        <h1 class="text-3xl font-bold mb-6 flex gap-4 justify-between items-center">
            Guild Wars 2 - Liste de tâches
            <div class="flex gap-2">
                <div class="tooltip tooltip-left" data-tip="Ajouter une tâche">
                    <label for="add-task" class="btn gap-1 btn-sm btn-square">
                        <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20" fill="currentColor" class="w-5 h-5">
                            <path d="M10.75 4.75a.75.75 0 00-1.5 0v4.5h-4.5a.75.75 0 000 1.5h4.5v4.5a.75.75 0 001.5 0v-4.5h4.5a.75.75 0 000-1.5h-4.5v-4.5z" />
                        </svg>
                    </label>
                </div>
                <div class="tooltip tooltip-left" data-tip="Exporter/importer">
                    <label for="export-task" class="btn btn-sm btn-square btn-outline">
                        <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20" fill="currentColor" class="w-5 h-5">
                            <path fill-rule="evenodd" d="M2.24 6.8a.75.75 0 001.06-.04l1.95-2.1v8.59a.75.75 0 001.5 0V4.66l1.95 2.1a.75.75 0 101.1-1.02l-3.25-3.5a.75.75 0 00-1.1 0L2.2 5.74a.75.75 0 00.04 1.06zm8 6.4a.75.75 0 00-.04 1.06l3.25 3.5a.75.75 0 001.1 0l3.25-3.5a.75.75 0 10-1.1-1.02l-1.95 2.1V6.75a.75.75 0 00-1.5 0v8.59l-1.95-2.1a.75.75 0 00-1.06-.04z" clip-rule="evenodd" />
                        </svg>                  
                    </label>
                </div>
            </div>
        </h1>

        {#if todolist.length > 0}

            <table class="table table-compact w-full shadow-xl rounded-xl">
                <thead>
                    <tr>
                        <th width="1">&nbsp;</th>
                        <th width="1">Heure</th>
                        <th width="1" class="text-center">TP</th>
                        <th>Tâche</th>
                        <th width="1">&nbsp;</th>
                    </tr>
                </thead>
                <tbody>
                    {#each todolist as task}
                        <tr>
                            <td>
                                <input type="checkbox" class="checkbox checkbox-xs" checked={(my.tasks.indexOf(task.id) >= 0)} on:change={updateMyTasks} data-task-id="{task.id}" /> 
                            </td>
                            <td class="whitespace-nowrap">{@html formatTime(task.time, task.moment)}</td>
                            <td class="text-center">
                                {#if task.wp}
                                    <button use:copy={task.wp} class="btn btn-xs btn-ghost">{task.wp}</button>
                                {:else}
                                    &nbsp;
                                {/if}
                            </td>
                            <td class="font-bold"><label for="{task.id}">{task.name}</label></td>
                            <td>
                                <button class="btn btn-error btn-xs btn-square btn-outline" on:click={removeTask} data-task-id="{task.id}">
                                    <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20" fill="currentColor" class="w-4 h-4">
                                        <path fill-rule="evenodd" d="M8.75 1A2.75 2.75 0 006 3.75v.443c-.795.077-1.584.176-2.365.298a.75.75 0 10.23 1.482l.149-.022.841 10.518A2.75 2.75 0 007.596 19h4.807a2.75 2.75 0 002.742-2.53l.841-10.52.149.023a.75.75 0 00.23-1.482A41.03 41.03 0 0014 4.193V3.75A2.75 2.75 0 0011.25 1h-2.5zM10 4c.84 0 1.673.025 2.5.075V3.75c0-.69-.56-1.25-1.25-1.25h-2.5c-.69 0-1.25.56-1.25 1.25v.325C8.327 4.025 9.16 4 10 4zM8.58 7.72a.75.75 0 00-1.5.06l.3 7.5a.75.75 0 101.5-.06l-.3-7.5zm4.34.06a.75.75 0 10-1.5-.06l-.3 7.5a.75.75 0 101.5.06l.3-7.5z" clip-rule="evenodd" />
                                    </svg>
                                </button>
                            </td>
                        </tr>
                    {/each}
                </tbody>
            </table>
        {:else}
            <div class="alert shadow-lg">
                <div>
                    <svg xmlns="http://www.w3.org/2000/svg" class="stroke-current flex-shrink-0 h-6 w-6" fill="none" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 9v2m0 4h.01m-6.938 4h13.856c1.54 0 2.502-1.667 1.732-3L13.732 4c-.77-1.333-2.694-1.333-3.464 0L3.34 16c-.77 1.333.192 3 1.732 3z" /></svg>
                    <span><strong>Aucune tâche trouvée.</strong> Pour créer ta première tâche, appuie sur le bouton "+ Nouveau".</span>
                </div>
            </div>
        {/if}
    </div>

    <input type="checkbox" id="add-task" class="modal-toggle" bind:checked={addTaskOpened} />
    <div class="modal">
        <div class="modal-box">
            <h3 class="font-bold text-lg">Ajouter une tâche</h3>
            <form class="" autocomplete="off" on:submit|preventDefault={addTask}>
                <div class="flex gap-4">
                    <div class="form-control w-full">
                        <label class="label label-text" for="time">Horraire</label>
                        <input type="time" class="input w-full input-bordered" id="time" name="time" />
                    </div>
                    <div class="form-control w-full">
                        <label class="label label-text" for="name">Moment</label>
                        <select class="select select-bordered" name="moment">
                            <option value="">Aucun</option>
                            <option value="day">Jour</option>
                            <option value="night">Nuit</option>
                        </select>
                    </div>
                </div>
                <div class="form-control w-full">
                    <label class="label label-text" for="wp">Point de passage</label>
                    <input type="text" class="input w-full input-bordered" id="wp" placeholder="[&BBEEAAA=]" name="wp" />
                </div>
                <div class="form-control w-full">
                    <label class="label label-text" for="name">Intitulé</label>
                    <input type="text" class="input w-full input-bordered" id="name" name="name" required />
                </div>
                <div class="flex gap-4 justify-between mt-4">
                    <button type="submit" class="btn btn-primary">Enregistrer</button>
                    <button type="reset" class="btn btn-ghost" on:click={resetAddTask}>Annuler</button>
                </div>
            </form>
        </div>
    </div>

    <input type="checkbox" id="export-task" class="modal-toggle" />
    <div class="modal">
        <div class="modal-box">
            <h3 class="font-bold text-lg">Exporter/importer</h3>
            {#if importError}
                <div class="alert alert-warning shadow-lg mt-4 mb-2">
                    <div>
                        <svg xmlns="http://www.w3.org/2000/svg" class="stroke-current flex-shrink-0 h-6 w-6" fill="none" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 9v2m0 4h.01m-6.938 4h13.856c1.54 0 2.502-1.667 1.732-3L13.732 4c-.77-1.333-2.694-1.333-3.464 0L3.34 16c-.77 1.333.192 3 1.732 3z" /></svg>
                        <span>Données invalides...</span>
                    </div>
                </div>
            {/if}
            <div class="form-control w-full">
                <label for="tasks" class="label label-text">Liste des tâches</label>
                <textarea name="tasks" id="tasks" class="textarea textarea-bordered" rows="5" value={exportTodolist(todolist)} on:input={importTodolist}></textarea>
            </div>
            <div class="flex gap-4 justify-between mt-4">
                <button class="btn" use:copy={exportTodolist(todolist)}>Copier</button>
                <label for="export-task" class="btn btn-ghost">Fermer</label>
            </div>
        </div>
    </div>

    
</main>

<style>
</style>
