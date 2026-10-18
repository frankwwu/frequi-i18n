<template>
  <div>
    <h1 class="mb-5">{{ $t('404 Error.') }}</h1>
    <p class="h4">{{ $t('Ahhhhhhhh! The page you are looking for does not exist.') }}</p>
    <p>
      Don't worry, you can head back to
      <RouterLink to="/"> <span>{{ $t('the main page') }}</span> </RouterLink>.
    </p>
  </div>
</template>
