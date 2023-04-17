<script>
  import EditorToolbar from '$lib/components/EditorToolbar.svelte';
  import PlainText from '$lib/components/PlainText.svelte';
  import RichText from '$lib/components/RichText.svelte';
  import { fetchJSON } from '$lib/util';
  import PrimaryButton from '$lib/components/PrimaryButton.svelte';
  import WebsiteNav from '$lib/components/WebsiteNav.svelte';
  import Modal from '$lib/components/Modal.svelte';
  import LoginMenu from '$lib/components/LoginMenu.svelte';
  import PostTeaser from '$lib/components/PostTeaser.svelte';
  import Footer from '$lib/components/Footer.svelte';
  import Image from '$lib/components/Image.svelte';
  import NotEditable from '$lib/components/NotEditable.svelte';
  import SectionLabel from '$lib/components/SectionLabel.svelte';
  import FeedEntry from '../lib/components/FeedEntry.svelte';
  import { goto } from '$app/navigation';

  export let data;
  $: currentUser = data.currentUser;

  // --------------------------------------------------------------------------
  // DEFAULT PAGE CONTENT - AJDUST TO YOUR NEEDS
  // --------------------------------------------------------------------------

  const BIO_PLACEHOLDER = `
		<p>You can write a short bio about yourself here. Possibly you want to include contact information.</p>
	`;

  let editable, title, bioTitle, bioPicture, bio, contact, showUserMenu;

  function initOrReset() {
    title = data.page?.title || 'Nils Kjellman';
    bioPicture = data.page?.bioPicture || '/images/person-placeholder.jpg';
    bioTitle = data.page?.bioTitle || "Hi, I'm Jane Doe - I care about X.";
    bio = data.page?.bio || BIO_PLACEHOLDER;
    contact = data.page?.contact || 'Reach me via email, instagram, or facebook';
    editable = false;
  }

  // --------------------------------------------------------------------------
  // Page logic
  // --------------------------------------------------------------------------

  function toggleEdit() {
    editable = true;
    showUserMenu = false;
  }

  async function savePage() {
    try {
      // Only persist the start page when logged in as an admin
      if (currentUser) {
        await fetchJSON('POST', '/api/save-page', {
          pageId: 'home',
          page: {
            title,
            bioPicture,
            bioTitle,
            bio,
            contact
          }
        });
      }
      editable = false;
    } catch (err) {
      console.error(err);
      alert('There was an error. Please try again.');
    }
  }

  initOrReset();
</script>

<svelte:head>
  <title>{title}</title>
  <meta name="robots" content="index, follow" />
</svelte:head>

{#if editable}
  <EditorToolbar {currentUser} on:cancel={initOrReset} on:save={savePage} />
{/if}

<WebsiteNav bind:showUserMenu {currentUser} bind:editable />

{#if showUserMenu}
  <Modal on:close={() => (showUserMenu = false)}>
    <form class="w-full block" method="POST">
      <div class="w-full flex flex-col space-y-4 p-4 sm:p-6">
        <PrimaryButton type="button" on:click={() => goto('/posts/new')}>New post</PrimaryButton>
        <PrimaryButton on:click={toggleEdit}>Edit page</PrimaryButton>
        <LoginMenu {currentUser} />
      </div>
    </form>
  </Modal>
{/if}

<div>
  <div class="max-w-screen-md mx-auto px-6 pt-12 sm:pt-24">
    <NotEditable {editable}>
      <svg
        class="w-14 sm:w-20 mx-auto pb-8"
        viewBox="0 0 46 88"
        fill="none"
        xmlns="http://www.w3.org/2000/svg"
      >
        <path
          d="M30.4545 3.09091V38H22.5455L9.93182 19.6591H9.72727V38H0.25V3.09091H8.29545L20.7045 21.3636H20.9773V3.09091H30.4545ZM36.5139 38V11.8182H45.923V38H36.5139ZM41.2184 9.09091C39.9457 9.09091 38.8548 8.67045 37.9457 7.82955C37.0366 6.98864 36.582 5.97727 36.582 4.79545C36.582 3.61364 37.0366 2.60227 37.9457 1.76136C38.8548 0.920454 39.9457 0.5 41.2184 0.5C42.5025 0.5 43.5934 0.920454 44.4911 1.76136C45.4002 2.60227 45.8548 3.61364 45.8548 4.79545C45.8548 5.97727 45.4002 6.98864 44.4911 7.82955C43.5934 8.67045 42.5025 9.09091 41.2184 9.09091ZM0.25 78V43.0909H9.72727V57.2045H10.2045L20.7045 43.0909H31.75L19.9545 58.6364L32.0227 78H20.7045L12.8636 64.9091L9.72727 69V78H0.25ZM36.1389 51.8182H45.548V78.8864C45.548 81.1818 45.065 82.9773 44.0991 84.2727C43.1445 85.5682 41.8036 86.483 40.0764 87.017C38.3491 87.5511 36.332 87.8182 34.0252 87.8182C33.6843 87.8182 33.3718 87.8125 33.0877 87.8011C32.7923 87.7898 32.4684 87.7727 32.1161 87.75V80.7955C32.3434 80.8182 32.5423 80.8352 32.7127 80.8466C32.8718 80.858 33.0366 80.8636 33.207 80.8636C34.332 80.8636 35.0991 80.6875 35.5082 80.3352C35.9286 79.9943 36.1389 79.4432 36.1389 78.6818V51.8182ZM40.8434 49.0909C39.5707 49.0909 38.4798 48.6705 37.5707 47.8295C36.6616 46.9886 36.207 45.9773 36.207 44.7955C36.207 43.6136 36.6616 42.6023 37.5707 41.7614C38.4798 40.9205 39.5707 40.5 40.8434 40.5C42.1275 40.5 43.2184 40.9205 44.1161 41.7614C45.0252 42.6023 45.4798 43.6136 45.4798 44.7955C45.4798 45.9773 45.0252 46.9886 44.1161 47.8295C43.2184 48.6705 42.1275 49.0909 40.8434 49.0909Z"
          fill="black"
        />
      </svg>
    </NotEditable>
    <!--    <h1 class="text-4xl md:text-7xl font-bold text-center stripes">
      <PlainText {editable} bind:content={title} />
    </h1>-->
  </div>
</div>

{#if data.posts.length > 0}
  <NotEditable {editable}>
    <div class="bg-white pb-10 sm:pb-16" id="posts">
      <div class="max-w-screen-md mx-auto px-6 pt-12 sm:pt-24">
        <SectionLabel>My posts</SectionLabel>
      </div>
      {#each data.posts as post, i}
        <PostTeaser {post} />
      {/each}
    </div>
  </NotEditable>
{/if}

{#if data.feedEntries.length > 0}
  <NotEditable {editable}>
    <div class="bg-white pb-10 sm:pb-16" id="feed">
      <div class="max-w-screen-md mx-auto px-6 pt-12 sm:pt-24">
        <SectionLabel>My feed</SectionLabel>
      </div>
      {#each data.feedEntries as feedEntry, i}
        <FeedEntry {feedEntry} />
      {/each}
    </div>
  </NotEditable>
{/if}

<!-- Bio -->
<div id="contact" class="bg-white pb-12 sm:pb-24">
  <div class="max-w-screen-md mx-auto px-6">
    <SectionLabel>Contact</SectionLabel>
    <div class="">
      <h1 class="text-3xl md:text-5xl font-bold">
        <PlainText {editable} bind:content={bioTitle} />
      </h1>
    </div>
    <div class="prose md:prose-lg pb-6">
      <RichText multiLine {editable} bind:content={bio} />
    </div>
  </div>
</div>

<Footer counter="/" {editable} />
