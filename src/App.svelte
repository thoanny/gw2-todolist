<script>
    const todolist = [
        {
            "id": "azerty",
            "name": "Nom de la tâche",
            "wp": "[&AZER3]",
            "time": "11:00",
            "moment": "day"
        },
        {
            "id": "azerty2",
            "name": "Nom de la tâche 2",
            "wp": "[&AZER1]",
            "time": "11:45",
            "moment": "night"
        },
        {
            "id": "azerty3",
            "name": "Nom de la tâche 3",
            "wp": "[&AZER969]",
            "time": "23:57",
            "moment": null
        },
    ];

    const local = {
        "date": "2022-11-17",
        "tasks": [
            'azerty'
        ]
    };

    function updateTask() {

    }
</script>

<main>
    <div class="container mx-auto my-6">

        <h1 class="text-2xl font-bold mb-4">Guild Wars 2 - Liste de tâches</h1>

        <div class="flex gap-2 p-3 shadow rounded-lg mb-4 border items-center">
            <input type="time" class="input w-full input-bordered" placeholder="Heure" />
            <input type="text" class="input w-full input-bordered" placeholder="Point de passage" />
            <input type="text" class="input w-full input-bordered" placeholder="Intitulé de la tâche" />
            <select class="select select-bordered">
                <option value="">Aucun</option>
                <option value="day">Jour</option>
                <option value="night">Nuit</option>
            </select>
            <button class="btn">Enregistrer</button>
        </div>

        <table class="table w-full">
            <thead>
                <tr>
                    <th>&nbsp;</th>
                    <th>Heure</th>
                    <th>TP</th>
                    <th>Titre</th>
                    <th>&nbsp;</th>
                </tr>
            </thead>
            <tbody>
                {#each todolist as task}
                    <tr>
                        <td><input type="checkbox" class="checkbox" checked={(local.tasks.indexOf(task.id) >= 0)} on:change={updateTask} /></td>
                        <td>{(!task.moment) ? task.time : 'XX:'+task.time.split(':')[1]+' ('+task.moment+')'}</td>
                        <td><button class="btn btn-xs">{task.wp}</button></td>
                        <td class="font-bold">{task.name}</td>
                        <td>
                            <button class="btn btn-error btn-xs text-white">
                                <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-4 h-4">
                                    <path stroke-linecap="round" stroke-linejoin="round" d="M14.74 9l-.346 9m-4.788 0L9.26 9m9.968-3.21c.342.052.682.107 1.022.166m-1.022-.165L18.16 19.673a2.25 2.25 0 01-2.244 2.077H8.084a2.25 2.25 0 01-2.244-2.077L4.772 5.79m14.456 0a48.108 48.108 0 00-3.478-.397m-12 .562c.34-.059.68-.114 1.022-.165m0 0a48.11 48.11 0 013.478-.397m7.5 0v-.916c0-1.18-.91-2.164-2.09-2.201a51.964 51.964 0 00-3.32 0c-1.18.037-2.09 1.022-2.09 2.201v.916m7.5 0a48.667 48.667 0 00-7.5 0" />
                                </svg>                              
                            </button>
                        </td>
                    </tr>
                {/each}
            </tbody>
        </table>
    </div>
    <!-- 
        <ul>
            <li>Créer un formulaire pour ajouter des tâches</li>
            <li>Horraire (XX ou H) + TP + Nom</li>
            <li>Case à cocher réinitialisation quoti</li>
            <li>Tableau de données</li>
            <li>Clipboardjs</li>
        </ul> 
    -->
</main>

<style>
</style>
