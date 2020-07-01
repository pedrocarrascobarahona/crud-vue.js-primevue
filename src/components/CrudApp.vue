<template>
  <div style="margin: 0 auto; width: 80%">
    <Toast />
    <Panel header="CRUD ">
      <Menubar :model="items" />
      <br />
      <DataTable :value="personas" :paginator="true" :rows="10" selectionMode="single" :selection.sync="selectedPersona" dataKey="id">
        <Column field="id" header="ID"></Column>
        <Column field="nombre" header="Nombre"></Column>
        <Column field="apellido" header="Apellido"></Column>
        <Column field="direccion" header="Dirección"></Column>
        <Column field="telefono" header="Teléfono"></Column>
      </DataTable>
    </Panel>
    <Dialog header="Crear Persona" :visible.sync="displayModal" :modal="true">
      <span class="p-float-label">
        <InputText id="nombre" type="text" v-model="persona.nombre" style="width: 100%" />
        <label for="nombre">Nombre</label>
      </span>
      <br />
      <span class="p-float-label">
        <InputText id="apellido" type="text" v-model="persona.apellido" style="width: 100%" />
        <label for="apellido">Apellido</label>
      </span>
      <br />
      <span class="p-float-label">
        <InputText id="direccion" type="text" v-model="persona.direccion" style="width: 100%" />
        <label for="direccion">Dirección</label>
      </span>
      <br />
      <span class="p-float-label">
        <InputText id="telefono" type="text" v-model="persona.telefono" style="width: 100%" />
        <label for="telefono">Teléfono</label>
      </span>
      <template #footer>
        <Button label="Guardar" icon="pi pi-check" @click="save" />
        <Button label="Cancelar" icon="pi pi-times" @click="closeModal" class="p-button-secondary" />
      </template>
    </Dialog>
  </div>
</template>

<script>
import PersonaService from "../service/PersonaService";
export default {
  name: "CrudApp",
  data() {
    return {
      personas: null,
      persona: {
        id: null,
        nombre: null,
        apellido: null,
        direccion: null,
        telefono: null
      },
      selectedPersona: {
        id: null,
        nombre: null,
        apellido: null,
        direccion: null,
        telefono: null
      },
      items: [
        {
          label: "Nuevo",
          icon: "pi pi-fw pi-plus",
          command: () => {
            this.showSaveModal();
          }
        },
        {
          label: "Editar",
          icon: "pi pi-fw pi-pencil",
          command: () => {
            this.showEditModal();
          }
        },
        {
          label: "Eliminar",
          icon: "pi pi-fw pi-trash",
          command: () => {
            this.delete();
          }
        },
        {
          label: "Refrescar",
          icon: "pi pi-fw pi-refresh",
          command: () => {
            this.getAll();
          }
        }
      ],
      displayModal: false
    };
  },
  personaService: null,
  created() {
    this.personaService = new PersonaService();
  },
  mounted() {
    this.getAll();
  },
  methods: {
    showSaveModal() {
      this.displayModal = true;
    },
    showEditModal() {
      this.persona = this.selectedPersona;
      this.displayModal = true;
    },
    getAll() {
      this.personaService.getAll().then(data => {
        this.personas = data.data;
      });
    },
    delete(){
      if (confirm("¿Está seguro de eliminar el registro?")){  
        this.personaService.delete(this.selectedPersona.id).then(data => {
          if(data.status === 200) {
            this.$toast.add({
              severity:'success', 
              summary: 'Atención!!', 
              detail:'Se eliminó correctamente', 
              life: 3000
            });
            this.getAll();
          }
        });
      }
    },
    save() {
      this.personaService.save(this.persona).then(data => {
        if (data.status === 200) {
          this.displayModal = false;
          this.persona = {
            nombre: null,
            apellido: null,
            direccion: null,
            telefono: null
          };
          this.getAll();
        }
      });
    },
    closeModal() {
      this.displayModal = false;
    }
  }
};
</script>

<style>
</style>