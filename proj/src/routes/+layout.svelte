<script>
  import "../app.css";
  import { Navbar, NavBrand, NavLi, NavUl, NavHamburger } from 'flowbite-svelte';
  import { DarkMode } from 'flowbite-svelte';
</script>

<div class="h-screen flex flex-col">
  <div class="p-3">
    <Navbar rounded >
      <span class="text-center text-xl font-semibold dark:text-white pl-2">OPMDS Classification</span>
      <div/>
      <DarkMode/>
    </Navbar>
  </div>
  <slot/>
</div>
