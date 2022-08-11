<template>
  <div class="all-page">
    <div class="d-grid p-3 about">
      <div class="my-name">
        <h1>Mahmoud</h1>
        <h1>Almorahlee</h1>
      </div>
      <img class="logo" src="@/assets/logo.svg" alt="VueJS logo" />
    </div>
  </div>
</template>

<style>
.all-page {
  min-height: calc(100vh - 120px);
  display: grid;
  place-items: center;
}

.about {
  display: grid;
  padding: 24px;
  gap: 1rem;
  grid-template-areas: "stack";
  filter: drop-shadow(0 0 0.5rem rgb(241, 243, 191));
}

.about > * {
  grid-area: stack;
}

.my-name {
  z-index: 99;
  align-self: center;
  justify-self: end;
}

h1 {
  font-weight: 700;
  color: rgb(241, 243, 99);
}
.logo {
  width: 20rem;
  height: 20rem;
}
</style>
