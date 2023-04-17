<span class="stripes">
  <slot />
</span>

<style>
  .stripes {
    font-weight: 900;
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
    background-size: var(--size) var(--size);
    background: linear-gradient(
      -45deg,
      var(--stripe-1) 25%,
      var(--stripe-2) 25%,
      var(--stripe-2) 50%,
      var(--stripe-1) 50%,
      var(--stripe-1) 75%,
      var(--stripe-2) 75%,
      var(--stripe-2)
    );
    background-position: 0 0;
    animation: stripeAnimation 1.8s linear infinite;
  }
  @keyframes stripeAnimation {
    100% {
      background-position: var(--size) 0, var(--size) 0, var(--size) 0;
    }
  }
</style>
