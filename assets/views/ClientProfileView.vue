<template>
  <main>
    <section>
      <div class="tableau-client">
        <div class="tableau-title">
          <div>
            <h3>Profil Client {{ this.nom }} {{ this.prenom }}</h3>
          </div>
          <div class="btn-actions">
            <router-link
              title="Editer client"
              v-bind:to="'/edit-client/' + idClient"
              ><button class="add">
                <span
                  ><svg
                    xmlns="http://www.w3.org/2000/svg"
                    width="24"
                    height="24"
                    viewBox="0 0 24 24"
                    fill="none"
                  >
                    <rect
                      opacity="0.3"
                      x="2"
                      y="2"
                      width="20"
                      height="20"
                      rx="5"
                      fill="currentColor"
                    ></rect>
                    <rect
                      x="10.8891"
                      y="17.8033"
                      width="12"
                      height="2"
                      rx="1"
                      transform="rotate(-90 10.8891 17.8033)"
                      fill="currentColor"
                    ></rect>
                    <rect
                      x="6.01041"
                      y="10.9247"
                      width="12"
                      height="2"
                      rx="1"
                      fill="currentColor"
                    ></rect>
                  </svg>
                </span>
                Editer
              </button>
            </router-link>
            <button
              class="delete"
              v-on:click="deleteClient(idClient)"
              title="Supprimer client"
            >
              Supprimer
            </button>
          </div>
        </div>
        <div class="tableau-content">
          <div class="row">
            <div class="label">Nom</div>
            <div class="info">{{ this.nom }}</div>
          </div>
          <div class="row">
            <div class="label">Prénom</div>
            <div class="info">{{ this.prenom }}</div>
          </div>
          <div class="row">
            <div class="label">Adresse</div>
            <div class="info">{{ this.adresse }}</div>
          </div>
          <div class="row">
            <div class="label">Commune</div>
            <div class="info">{{ this.commune }}</div>
          </div>
          <div class="row">
            <div class="label">Code Postal</div>
            <div class="info">{{ this.codePostal }}</div>
          </div>
          <div class="row">
            <div class="label">Téléphone</div>
            <div class="info">{{ this.telephone }}</div>
          </div>
          <div class="row">
            <div class="label">Email</div>
            <div class="info">{{ this.email }}</div>
          </div>
          <div class="row">
            <div class="label">Raison Social</div>
            <div class="info">{{ this.raison }}</div>
          </div>
        </div>
      </div>
    </section>
    <router-view :clientDataChantier="this.chantier_data" :clientDataDepannage="this.depannage_data"></router-view>
  </main>
</template>

<style lang="scss" scoped>
@import "@/styles/clientProfile.scss";
@import "@/styles/viewList.scss";
</style>

<script>
export default {
  props: ["idClient"],
  data() {
    return {
      data: null,
      nom: null,
      prenom: null,
      commune: null,
      adresse: null,
      codePostal: null,
      telephone: null,
      email: null,
      raison: null,
      chantier_data: null,
      depannage_data: null,
    };
  },
  methods: {
    async getClient(idClient) {
      const headers = new Headers({
        "Content-Type": "application/json",
        Accept: "application/json",
      });
      let getData = {
        method: "GET",
        headers: headers,
        mode: "cors",
        cache: "default",
      };
      try {
        let response = await fetch(`/api/clients/${idClient}`, getData);
        if (response.status === 200) {
          let data = await response.json();
          this.chantier_data = data.chantier;
          this.depannage_data = data.depannages;
          console.log("Success:", data);
          this.nom = data.nom;
          this.prenom = data.prenom;
          this.commune = data.ville;
          this.adresse = data.adresse;
          this.codePostal = data.codePostal;
          this.telephone = data.telephone;
          this.email = data.email;
          this.raison = data.raisonSociale;
        }
        if (response.status === 404) {
          this.$router.push("/404");
        }
      } catch (err) {
        console.error(err);
      }
    },
    formatDate(value) {
      if (value) {
        let date = new Date(value);
        return date.toLocaleDateString();
      }
    },
    async deleteClient(id) {
      const deleteHeaders = new Headers({
        "Content-Type": "application/json",
        Accept: "application/json",
      });
      let deletetData = {
        method: "DELETE",
        headers: deleteHeaders,
        mode: "cors",
        cache: "default",
      };
      try {
        let response = await fetch("/api/clients/" + id, deletetData);

        if (response.status === 204) {
          console.log("Success");
          this.$router.push("/list-clients");
        }
      } catch (error) {
        console.error("Error:", error);
      }
    },
  },
  mounted() {
    this.getClient(this.idClient);
  },
};
</script>
