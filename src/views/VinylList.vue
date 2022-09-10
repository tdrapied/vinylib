<template>
  <CustomLoader />
  <DashboardBase>
    <header>
      <div class="max-w-7xl mx-auto py-10 px-4 sm:px-6 lg:px-8">
        <div class="flex justify-between">
          <div>
            <h1 class="text-4xl font-bold text-black">Ma librairie</h1>
            <h2 class="mt-2 font-light text-gray-500">
              {{ vinylCountString }}
            </h2>
          </div>
          <div v-if="items.length > 0">
            <router-link
              :to="{ name: 'vinyl-create' }"
              class="inline-flex items-center py-2 px-4 border border-transparent text-lg font-medium rounded-md text-white bg-primary focus:outline-none"
            >
              <PlusIcon class="w-6 h-6 mr-1" />
              Ajouter un vinyle
            </router-link>
          </div>
        </div>
      </div>
    </header>
    <main>
      <div class="max-w-7xl mx-auto sm:px-6 lg:px-8">
        <div class="px-4 sm:px-0 pb-10">
          <table class="table-auto w-full" v-if="items.length > 0">
            <thead>
              <tr>
                <th class="text-center font-normal pb-3">#</th>
                <th class="text-left font-normal pb-3">VINYLE</th>
                <th class="text-left font-normal pb-3 hidden sm:table-cell">
                  DATE DE SORTIE
                </th>
                <th class="text-left font-normal pb-3 hidden lg:table-cell">
                  AJOUTÉ LE
                </th>
                <th></th>
              </tr>
            </thead>
            <tbody>
              <ItemRow
                v-for="(item, index) in items"
                :key="item.id"
                :number="index + 1"
                :vinyl="item"
              />
            </tbody>
          </table>
          <div class="mt-10 text-center" v-if="items.length === 0">
            <div>
              <div class="text-2xl font-bold">Votre librairie est vide.</div>
              <div class="mt-3 font-light text-gray-400">
                On dirait que vous n'avez pas de vynile ici. Ajoutez-en !
              </div>
            </div>
            <div class="mt-8 text-center">
              <router-link
                :to="{ name: 'vinyl-create' }"
                class="inline-flex items-center py-2 px-4 border border-transparent text-lg font-medium rounded-md text-white bg-primary focus:outline-none"
              >
                <PlusIcon class="w-6 h-6 mr-1" />
                Ajouter un vinyle
              </router-link>
            </div>
          </div>
        </div>
      </div>
    </main>
  </DashboardBase>
</template>

<script>
import CustomLoader from "@/components/CustomLoader";
import DashboardBase from "@/components/DashboardBase";
import ItemRow from "@/components/ItemRow";
import { PlusIcon } from "@heroicons/vue/outline";
import { HTTP } from "@/config/http-common";

export default {
  name: "VinylList",
  components: {
    CustomLoader,
    DashboardBase,
    ItemRow,
    PlusIcon,
  },
  data() {
    return {
      items: [],
    };
  },
  computed: {
    vinylCountString() {
      switch (this.items.length) {
        case 0:
          return "Aucun vinyle enregistré";
        case 1:
          return "1 vinyle enregistré";
        default:
          return `${this.items.length} vinyles enregistrés`;
      }
    },
  },
  async mounted() {
    HTTP.get("/vinyls")
      .then((res) => {
        this.items = res.data;
        this.$store.commit("disableLoading");
      })
      .catch((e) => {
        if (e.response.status === 401) {
          this.$router.push({ name: "login" });
        }
      });
  },
};
</script>