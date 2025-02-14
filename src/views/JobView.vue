<script setup>
import { reactive, onMounted } from "vue";
import { useRoute, RouterLink, useRouter } from "vue-router";
import { useToast } from "vue-toastification";
import BackButton from "@/components/BackButton.vue";
import PulseLoader from "vue-spinner/src/PulseLoader.vue";

const route = useRoute();
const router = useRouter();
const toast = useToast();
const jobId = route.params.id;
const state = reactive({ job: {}, isLoading: true });

async function getJobsDataById() {
  const url = `/api/jobs/${jobId}`;
  try {
    const response = await fetch(url);
    if (!response.ok) throw new Error(`Response status: ${response.status}`);
    const json = await response.json();
    state.job = json;
  } catch (error) {
    console.error(`Error fetching job with ID ${jobId}:`, error.message);
  } finally {
    state.isLoading = false;
  }
}

const deleteJob = async () => {
  try {
    const confirm = window.confirm("Are you sure you want to delete this job?");
    if (confirm) {
      const response = await fetch(`/api/jobs/${jobId}`, { method: "DELETE" });
      if (!response.ok) throw new Error(`Response status: ${response.status}`);
      toast.success("Job deleted successfully");
      router.push("/jobs");
    }
  } catch (error) {
    toast.error(`Failed to delete job with id ${jobId}:`, error);
  }
};

onMounted(getJobsDataById);
</script>

<template>
  <section
    class="bg-gradient-to-br from-blue-50 to-indigo-50 min-h-screen pt-24"
  >
    <div class="container mx-auto px-4 pb-12 sm:pb-24">
      <BackButton />

      <div v-if="state.isLoading" class="text-center py-8">
        <PulseLoader color="#3B82F6" />
      </div>

      <div v-else>
        <!-- Hero Section -->
        <header
          class="relative rounded-2xl bg-gradient-to-r from-blue-600 to-indigo-600 p-8 md:p-12 mb-8 overflow-hidden"
        >
          <div
            class="absolute inset-0 bg-grid-white/[0.2] bg-[size:20px_20px]"
          ></div>
          <!-- Quick Actions - Added to top right -->
          <div class="relative flex justify-between items-start">
            <div class="flex-1">
              <span
                class="px-4 py-1.5 text-sm font-medium rounded-full inline-block bg-white/20 text-white backdrop-blur-sm mb-4"
              >
                {{ state.job.type }}
              </span>
              <h1 class="text-4xl md:text-5xl font-bold text-white mb-4">
                {{ state.job.title }}
              </h1>
              <div class="flex flex-wrap items-center gap-6 text-blue-100">
                <div class="flex items-center">
                  <i class="mdi mdi-map-marker mr-2"></i>
                  {{ state.job.location }}
                </div>
                <div class="flex items-center">
                  <i class="mdi mdi-currency-usd mr-2"></i>
                  {{ state.job.salary }} / year
                </div>
                <div class="flex items-center">
                  <i class="mdi mdi-office-building mr-2"></i>
                  {{ state.job.company.name }}
                </div>
              </div>
            </div>
            <div class="flex gap-2">
              <RouterLink
                :to="`/jobs/edit/${state.job.id}`"
                class="p-2 rounded-lg bg-white/20 hover:bg-white/30 transition-colors duration-200 backdrop-blur-sm text-white"
                title="Edit Job"
              >
                <i class="mdi mdi-pencil text-xl"></i>
              </RouterLink>
              <button
                @click="deleteJob"
                class="p-2 rounded-lg bg-white/20 hover:bg-white/30 transition-colors duration-200 backdrop-blur-sm text-white"
                title="Delete Job"
              >
                <i class="mdi mdi-delete text-xl"></i>
              </button>
            </div>
          </div>
        </header>

        <!-- Content Grid -->
        <div class="grid md:grid-cols-3 gap-8">
          <!-- Main Content -->
          <section class="md:col-span-2">
            <!-- Job Description -->
            <article
              class="bg-white rounded-2xl p-8 shadow-sm border border-gray-100"
            >
              <h2 class="text-2xl font-bold text-gray-900 mb-6">
                About This Role
              </h2>
              <div
                class="prose prose-blue max-w-none text-gray-600 whitespace-pre-line"
              >
                {{ state.job.description }}
              </div>
            </article>
          </section>

          <!-- Sidebar -->
          <aside class="space-y-6">
            <!-- Company Card -->
            <article
              class="bg-white rounded-2xl p-8 shadow-sm border border-gray-100"
            >
              <h3 class="text-2xl font-bold text-gray-900 mb-6">
                About the Company
              </h3>
              <div class="prose prose-blue max-w-none text-gray-600 mb-6">
                {{ state.job.company.description }}
              </div>

              <!-- Contact Information -->
              <div class="space-y-1">
                <div class="flex items-center gap-2">
                  <i
                    class="mdi mdi-email text-blue-600 text-xl mt-0.5 shrink-0"
                  ></i>
                  <div class="min-w-0">
                    <a
                      :href="`mailto:${state.job.company.contactEmail}`"
                      class="text-blue-600 hover:text-blue-800 break-all"
                    >
                      {{ state.job.company.contactEmail }}
                    </a>
                  </div>
                </div>
                <a
                  v-if="state.job.company.contactPhone"
                  :href="`tel:${state.job.company.contactPhone}`"
                  class="flex items-center text-blue-600 hover:text-blue-700"
                >
                  <i class="mdi mdi-phone mr-2"></i>
                  {{ state.job.company.contactPhone }}
                </a>
              </div>
            </article>

            <!-- Apply Now Card -->
            <div
              class="bg-gradient-to-br from-blue-600 to-indigo-600 rounded-2xl p-8 text-white"
            >
              <h3 class="text-xl font-bold mb-4">Ready to Apply?</h3>
              <p class="text-blue-100 mb-6">
                Take the next step in your career journey and apply for this
                position.
              </p>
              <a
                :href="`mailto:${state.job.company.contactEmail}?subject=Application for ${state.job.title}`"
                class="block w-full bg-white text-blue-600 px-6 py-3 rounded-xl font-medium hover:bg-blue-50 transition-colors duration-200 text-center"
              >
                Apply Now
              </a>
            </div>
          </aside>
        </div>
      </div>
    </div>
  </section>
</template>
