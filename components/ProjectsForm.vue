<script setup lang="ts">
const form = ref({
  title: '',
  description: '',
  image: '',
  categoryUuid: '',
  softCap: 10000,
  hardCap: 25000,
  startAt: new Date().toISOString().slice(0, 10),
  finishesAt: getDateXMonthsFromNow(6).toISOString().slice(0, 10),
});

const selectedCategory = computed(() =>
  categories.value.find(category => category.uuid === form.value.categoryUuid)
);

// Get categories for dropdown
const { list: categories, fetchAll } = useCategories();

const submitForm = () => {
  useAlerts().success("Project created");
};

const shortenDescription = (description: string) => {
  const ellipsis = description.length > 130 ? '...' : '';
  return `${description.slice(0, 130)}${ellipsis}`;
}

fetchAll();

watch(() => form.value.softCap, (newSoftCap) => {
  if (newSoftCap > form.value.hardCap) {
    form.value.hardCap = newSoftCap;
  }
});

watch(() => form.value.hardCap, (newHardCap) => {
  if (newHardCap < form.value.softCap) {
    form.value.softCap = newHardCap;
  }
});

</script>

<template>
  <div class="w-full">
    <h1 class="text-3xl font-semibold mb-6">
      Kickstart your own project
    </h1>
    <div class="grid grid-cols-12 gap-8">
      <main class="col-span-8">
        <form @submit.prevent="submitForm">
          <ProjectsFormField
            name="title"
            label="What is your project's name?"
            hint="Use a very handy title that people could identify  your project"
          >
            <input
              v-model="form.title"
              id="title"
              class="form-field"
            />
          </ProjectsFormField>

          <ProjectsFormField
            name="description"
            label="What is your project about?"
            hint="Describe with full detail your projecty so that people understand exactly what it is about"
          >
            <textarea
              v-model="form.description"
              id="description"
              class="form-field min-h-[300px]"
            />
          </ProjectsFormField>

          <AppFileUpload
            bucket="projects"
            @file:uploaded="form.image = $event"
            class="mb-4"
          />

          <ProjectsFormField
            name="category"
            label="Which category does your project fit in?"
            hint="Selecting a fitting category ensures the right people find your project"
          >
            <select
              v-model="form.categoryUuid"
              id="category"
              class="form-field select"
            >
              <option
                v-for="category in categories"
                :key="category.uuid"
                :value="category.uuid"
              >
                {{ category.name }}
              </option>
            </select>
          </ProjectsFormField>

          <ProjectsFormField
            name="softCap"
            label="What is the soft cap of your project?"
            hint="Soft cap is the minimum amount of money that you need to raise in order to start your project"
          >
            <div class="w-full font-semibold">
              <input
                v-model.number="form.softCap"
                id="softCap"
                type="range"
                min="0"
                max="100000"
                class="range"
                step="5000"
              />
              <ProjectsFormRangeHint :amounts="[10000, 25000, 50000, 75000, 100000]" />
            </div>
          </ProjectsFormField>

          <ProjectsFormField
            name="hardCap"
            label="What is the hard cap of your project?"
            hint="Hard cap is the maximum amount of money that you need to raise in order to start your project"
          >
            <div class="w-full font-semibold">
              <input
                v-model.number="form.hardCap"
                id="hardCap"
                type="range"
                min="0"
                max="100000"
                class="range"
                step="5000"
              />
              <ProjectsFormRangeHint :amounts="[10000, 25000, 50000, 75000, 100000]" />
            </div>
          </ProjectsFormField>

          <ProjectsFormField
            name="startAt"
            label="What is the start date of your project?"
            hint="Start date of your project"
          >
            <div class="w-full font-semibold">
              <input
                v-model="form.startAt"
                id="startAt"
                type="date"
                class="form-field"
              />
            </div>
          </ProjectsFormField>

          <ProjectsFormField
            name="endDate"
            label="What is the end date of your project?"
            hint="End date of your project"
          >
            <div class="w-full font-semibold">
              <input
                v-model="form.finishesAt"
                id="endDate"
                type="date"
                class="form-field"
              />
            </div>
          </ProjectsFormField>
          <div class="flex justify-end">
            <button class="btn btn-primary">Publish your project</button>
          </div>
        </form>
      </main>
      <aside class="col-span-4">
        <ProjectCard
          :project="{
            image: form.image || 'https://placehold.co/500x320',
            title: form.title || 'Your Title Here',
            excerpt: form.description
              ? `${shortenDescription(form.description)}`
              : 'This is a description of your project. You can change it in the form. You have up to 130 characters to describe your project.',
            backers: 0,
            pledged: 0,
            hardCap: form.hardCap.toString(),
            softCap: form.softCap.toString(),
            finishesAt: form.finishesAt.toString(),
          }"
          :category-name="selectedCategory?.name ?? 'Some category'"
        />
      </aside>
    </div>
  </div>
</template>

<style lang="postcss">
.form-field {
  @apply px-4 rounded-3xl bg-transparent input input-bordered w-full;
}
</style>
