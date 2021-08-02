<template>

    <div>

        <form @submit.prevent="update()" v-if="isEdit">
            <h3>Editar Nota</h3>
            <input type="text" placeholder="Nombre" class="form-control mb-2" v-model="note.name">
            <input type="text" placeholder="Descripción" class="form-control mb-2" v-model="note.description">
            <button type="submit" class="btn btn-warning">
                Actualizar
            </button>
            <button type="button" class="btn btn-danger" @click="clear()">
                Cancelar
            </button>
        </form>

        <form @submit.prevent="add()" v-else>
            <h3>Agregar Nota</h3>
            <input type="text" placeholder="Nombre" class="form-control mb-2" v-model="note.name">
            <input type="text" placeholder="Descripción" class="form-control mb-2" v-model="note.description">
            <button type="submit" class="btn btn-success">
                Guardar
            </button>
        </form>

        <h3 class="mt-3">Lista de notas</h3>
        <ul class="list-group">
            <li class="list-group-item" v-for="(note, index) in notes" :key="index">
                <span class="badge badge-primary float-right">{{ note.updated_at }}</span>
                <p>{{ note.name }}</p>
                <p>{{ note.description }}</p>
                <button class="btn btn-danger btn-sm float-right mx-1"
                    @click="remove(note, index)">Eliminar</button>
                <button class="btn btn-warning btn-sm float-right mx-1"
                    @click="edit(note, index)">Actualizar</button>
            </li>
        </ul>
    </div>
</template>

<script>
export default {
    data() {
        return {
            notes: [],
            note: {
                name: null,
                description: null
            },
            isEdit: false
        }
    },
    created() {
        axios.get('notas')
            .then((res) => {
                this.notes = res.data;
            });
    },
    methods: {
        add() {
            const params = {
                name: this.note.name,
                description: this.note.description,
            };

            axios.post('notas', params)
                .then((res) => {
                    this.notes.push(res.data);
                    this.clear();
                });
        },
        remove(note, index) {
            axios.delete(`notas/${note.id}`).then(() => {
                alert('Eliminado con éxito.');
                this.notes.splice(index, 1);
            });
        },
        edit(note) {
            this.isEdit = true;
            this.note.name = note.name;
            this.note.description = note.description;
            this.note.id = note.id;
        },
        update() {
            const params = {
                name: this.note.name,
                description: this.note.description,
            };

            axios.put(`notas/${this.note.id}`, params)
            .then((res) => {
                const index = this.notes.findIndex(note => note.id === res.data.id);
                this.notes[index] = res.data;
                this.clear();
            })
        },
        clear() {
            this.note.name = null;
            this.note.description = null;
            this.note.id = null;
            this.isEdit = false;
        },
    }
}
</script>
