<template>
  <div>
    <NuxtPage />
  </div>
</template>
<style>
@media (prefers-color-scheme: dark) {
  :root {
    --background-color: #002b36;
    --text-color: #FDF6E3;
    --primary-color: #ff5722;
    --primary-hightlight-color: #ff8a65;
    --secondary-color: #ff9800;
    --secondary-hightlight-color: #ffc947;
  }
}

@media (prefers-color-scheme: light) {

  :root {
    --background-color: #FDF6E3;
    --text-color: #002b36;
    --primary-color: #ff5722;
    --primary-hightlight-color: #ff8a65;
    --secondary-color: #ff9800;
    --secondary-hightlight-color: #ffc947;
  }
}

* {
    box-sizing: border-box;
}
body {
  background-color: var(--background-color);
  color: var(--text-color);
  margin:0;
}
</style>
