<template>
  <div class="ms_content">
    <nav id="bd-docs-nav" aria-label="Main navigation" class="bd-links">
      <ul class="nav d-block">
        <li class="nav-item"><NuxtLink to="/characters">Characters</NuxtLink></li>
        <li class="nav-item"><NuxtLink to="/locations">Locations</NuxtLink></li>
        <li class="nav-item"><NuxtLink to="/episodes">Episodes</NuxtLink></li>
      </ul>
    </nav>
  </div>
</template>

<script>
export default {
  name: 'SideBar'
}
</script>