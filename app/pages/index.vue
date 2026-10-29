<script setup lang="ts">
import csuLogo from "~/assets/csumain.png";
import duquesaPhoto from "~/assets/Duquesa.jpg";
import { computed, onBeforeUnmount, onMounted, ref } from "vue";

type PageSection = "values" | "university" | "announcements" | "profile";
type Filter = "All" | "Academic" | "Campus" | "Services" | "Activity";

const mobileMenuOpen = ref(false);
const activeSection = ref<PageSection>("values");
const pageScroller = ref<HTMLElement | null>(null);
const searchTerm = ref("");
const selectedFilter = ref<Filter>("All");
const filters: Filter[] = ["All", "Academic", "Campus", "Services", "Activity"];
const navLinks: { id: PageSection; label: string; number: string }[] = [
  { id: "values", label: "Core Values", number: "01" },
  { id: "university", label: "About CSU", number: "02" },
  { id: "announcements", label: "Announcements", number: "03" },
  { id: "profile", label: "Student Profile", number: "04" },
];

const coreValues = [
  {
    number: "01",
    title: "Competence",
    icon: "terminal",
    code: "skill.execute()",
    description:
      "Learn deeply, think critically, and develop the skills to solve real-world challenges.",
    symbol: "</>",
  },
  {
    number: "02",
    title: "Service",
    icon: "message",
    code: "impact.create()",
    description:
      "Use knowledge with purpose and contribute meaningfully to the community.",
    symbol: "{}",
  },
  {
    number: "03",
    title: "Uprightness",
    icon: "shield",
    code: "ethics.validate()",
    description:
      "Practice honesty, accountability, and integrity in every decision.",
    symbol: "✓",
  },
];

// DEMONSTRATION CONTENT ONLY. These are not actual or current CSU announcements.
const notices: {
  id: number;
  type: Exclude<Filter, "All">;
  eyebrow: string;
  title: string;
  summary: string;
  href: string;
  icon: string;
}[] = [
  {
    id: 1,
    type: "Academic",
    eyebrow: "STUDENT GUIDE / SAMPLE",
    title: "Academic records & requests",
    summary:
      "A guide card showing where students can check official academic-record procedures and availability.",
    href: "https://myschool.carsu.edu.ph/",
    icon: "file",
  },
  {
    id: 2,
    type: "Campus",
    eyebrow: "CAMPUS UPDATES",
    title: "University news & activities",
    summary:
      "A proposed spot for campus-wide stories, events, and student opportunities sourced from official channels.",
    href: "https://www.carsu.edu.ph/",
    icon: "megaphone",
  },
  {
    id: 3,
    type: "Services",
    eyebrow: "STUDENT SUPPORT",
    title: "MySchool account assistance",
    summary:
      "A shortcut concept for helping students reach the existing MySchool support channels.",
    href: "https://myschool.carsu.edu.ph/",
    icon: "help",
  },
  {
    id: 4,
    type: "Academic",
    eyebrow: "CCIS BULLETIN",
    title: "BSIT section bulletin",
    summary:
      "For more information on BSIT 3 - CCIS, students can check the official College of Computing and Information Sciences official facebook page for updates and announcements.",
    href: "https://www.facebook.com/cciscarsu",
    icon: "megaphone",
  },

  {
    id: 5,
    type: "Activity",
    eyebrow: "SANGKA 2026 / CSU USG",
    title: "SANGKA 2026 Updates",
    summary:
      "Stay updated with SANGKA 2026 announcements, activities, and event information through the official CSU Main University Student Government Facebook page.",
    href: "https://www.facebook.com/csumain.usg",
    icon: "megaphone",
  },
];

const filteredNotices = computed(() =>
  notices.filter((notice) => {
    const categoryMatches =
      selectedFilter.value === "All" || notice.type === selectedFilter.value;
    const query = searchTerm.value.trim().toLowerCase();
    const searchMatches =
      !query ||
      `${notice.title} ${notice.summary} ${notice.type}`
        .toLowerCase()
        .includes(query);
    return categoryMatches && searchMatches;
  }),
);

let observer: IntersectionObserver | undefined;

function jumpTo(id: PageSection) {
  activeSection.value = id;
  mobileMenuOpen.value = false;
  if (import.meta.client)
    document
      .getElementById(id)
      ?.scrollIntoView({ behavior: "smooth", block: "start" });
}

onMounted(() => {
  if (!("IntersectionObserver" in window)) return;
  observer = new IntersectionObserver(
    (entries) => {
      entries.forEach((entry) => {
        if (entry.isIntersecting)
          activeSection.value = entry.target.id as PageSection;
      });
    },
    {
      root: pageScroller.value,
      rootMargin: "-15% 0px -60% 0px",
      threshold: 0,
    },
  );
  for (const item of navLinks) {
    const el = document.getElementById(item.id);
    if (el) observer.observe(el);
  }
});
onBeforeUnmount(() => observer?.disconnect());

useSeoMeta({
  title: "CSU MySchool – Student Landing Page",
  description:
    "A modern, student-focused Nuxt and Tailwind CSS landing page concept for Caraga State University, BSIT 3 - CCIS. In compliance of Laboratory Activity in ITE 18.",
});
</script>

<template>
  <div class="app-frame text-slate-100">
    <!-- Constant-height header. The mobile menu is an overlay, so the bar never expands. -->
    <header
      class="site-header relative z-50 flex h-[68px] shrink-0 items-center border-b border-white/10 bg-[#080e19]/95 backdrop-blur-xl"
    >
      <div
        class="mx-auto flex w-full max-w-[1450px] items-center justify-between gap-4 px-5 sm:px-8 lg:px-12"
      >
        <button
          type="button"
          class="group inline-flex min-w-0 items-center gap-3 text-left"
          aria-label="Go to Core Values"
          @click="jumpTo('values')"
        >
          <!-- Complete Caraga State University Logo -->
          <span
            class="flex h-[58px] w-[58px] shrink-0 items-center justify-center"
          >
            <img
              :src="csuLogo"
              alt="Complete Caraga State University emblem"
              class="h-full w-full object-contain"
              width="58"
              height="58"
            />
          </span>
          <span class="block min-w-0 leading-none">
            <span
              class="block truncate text-[16px] font-extrabold tracking-[-.04em]"
            >
              MySchool<span class="text-emerald-400">.next</span>
            </span>
            <span
              class="mt-1.5 block truncate text-[9px] font-semibold tracking-[.23em] text-slate-400"
            >
              CSU · STUDENT CONCEPT
            </span>
          </span>
        </button>

        <nav
          class="hidden items-center gap-1 lg:flex"
          aria-label="Main navigation"
        >
          <button
            v-for="link in navLinks"
            :key="link.id"
            type="button"
            class="nav-link"
            :class="{ 'nav-active': activeSection === link.id }"
            @click="jumpTo(link.id)"
          >
            <span class="mr-1.5 text-[10px] opacity-55">{{ link.number }}</span
            >{{ link.label }}
          </button>
        </nav>

        <div class="flex shrink-0 items-center gap-3">
          <span
            class="hidden items-center gap-2 rounded-full border border-emerald-400/20 bg-emerald-400/[.07] px-3 py-2 text-[10px] font-semibold tracking-[.14em] text-emerald-300 sm:inline-flex"
            ><span class="status-dot"></span> BSIT 3 · ITE18 - AIEM1</span
          >
          <a
            href="https://myschool.carsu.edu.ph/"
            target="_blank"
            rel="noopener noreferrer"
            class="hidden h-9 items-center gap-2 rounded-lg border border-emerald-300/30 bg-emerald-400 px-3.5 text-xs font-extrabold text-[#062119] transition hover:bg-emerald-300 sm:inline-flex"
            >Official Portal <AppIcon name="up" :size="10"
          /></a>
          <button
            type="button"
            class="grid h-10 w-10 place-items-center rounded-lg border border-white/15 text-slate-200 transition hover:border-emerald-400/40 lg:hidden"
            :aria-expanded="mobileMenuOpen"
            aria-label="Toggle navigation"
            @click="mobileMenuOpen = !mobileMenuOpen"
          >
            <AppIcon :name="mobileMenuOpen ? 'x' : 'menu'" :size="21" />
          </button>
        </div>
      </div>
      <nav
        v-if="mobileMenuOpen"
        class="absolute left-0 right-0 top-full border-b border-white/10 bg-[#0b1423] p-4 shadow-2xl lg:hidden"
        aria-label="Mobile navigation"
      >
        <button
          v-for="link in navLinks"
          :key="link.id"
          type="button"
          class="flex w-full items-center gap-4 rounded-lg px-4 py-3 text-left text-sm text-slate-200 hover:bg-white/5"
          @click="jumpTo(link.id)"
        >
          <span class="text-xs text-emerald-400">{{ link.number }}</span
          >{{ link.label }}
        </button>
        <a
          href="https://myschool.carsu.edu.ph/"
          target="_blank"
          rel="noopener noreferrer"
          class="mt-2 flex items-center justify-between rounded-lg bg-emerald-400 px-4 py-3 text-sm font-bold text-[#062219]"
          >Official MySchool <AppIcon name="up" :size="17"
        /></a>
      </nav>
    </header>

    <!-- Only this region scrolls. Header/footer stay a fixed height and never overlap content. -->
    <main
      ref="pageScroller"
      id="page-scroll"
      class="relative min-h-0 flex-1 overflow-y-auto overscroll-contain scroll-smooth"
    >
      <!-- STYLE 01 / Core values: bold neon hero + three feature tiles -->
      <section
        id="values"
        class="section-panel hero-pattern relative isolate overflow-hidden border-b border-white/[.07]"
      >
        <div
          class="pointer-events-none absolute -right-36 -top-56 h-[650px] w-[650px] rounded-full bg-emerald-600/[.12] blur-[110px]"
        ></div>
        <div
          class="pointer-events-none absolute -left-56 top-40 h-[500px] w-[500px] rounded-full bg-cyan-600/[.08] blur-[120px]"
        ></div>
        <div
          class="relative mx-auto max-w-[1310px] px-5 pb-19 pt-12 sm:px-8 lg:px-10 lg:pt-[76px]"
        >
          <div class="grid items-center gap-14 lg:grid-cols-[1.05fr_.95fr]">
            <div class="relative z-10">
              <div class="section-kicker">
                <span
                  class="inline-block h-[6px] w-[6px] rounded-full bg-emerald-400"
                ></span>
                CARAGA STATE UNIVESITY - MySchool
              </div>
              <h1
                class="mt-7 text-[clamp(3.2rem,7vw,6.75rem)] font-black leading-[.98] tracking-[-.085em] text-[#eef8f5]"
              >
                A smarter<br /><span class="headline-gradient">campus</span
                ><br />starts here<span class="text-emerald-400">.</span>
              </h1>
              <p
                class="mt-7 max-w-[525px] text-[15px] leading-8 text-[#a9b8c7] sm:text-[17px]"
              >
                Brgy. Ampayon, Butuan City, 8600 Agusan Del Norte Philippines
              </p>
              <div class="mt-8 flex flex-wrap gap-3">
                <button
                  class="action-main"
                  type="button"
                  @click="jumpTo('university')"
                >
                  Discover CSU <AppIcon name="arrow" :size="18" /></button
                ><button
                  type="button"
                  class="action-outline"
                  @click="jumpTo('announcements')"
                >
                  View announcements <AppIcon name="chevron" :size="17" />
                </button>
              </div>
              <div
                class="mt-12 flex items-center gap-3 text-[11px] font-semibold tracking-[.15em] text-slate-500"
              >
                <span class="h-px w-9 bg-emerald-400"></span> College of
                Computing and Information Sciences
              </div>
            </div>

            <div
              class="relative mx-auto w-full max-w-[550px]"
              aria-label="Decorative MySchool student system preview"
            >
              <div
                class="absolute -inset-6 rounded-[34px] bg-emerald-500/[.075] blur-3xl"
              ></div>
              <div
                class="terminal-shell relative overflow-hidden rounded-3xl border border-white/[.13] bg-[#0d1928]/95 shadow-[0_36px_90px_rgba(0,0,0,.45)]"
              >
                <div
                  class="flex h-12 items-center justify-between border-b border-white/10 px-5"
                >
                  <div class="flex gap-2">
                    <span
                      class="h-[9px] w-[9px] rounded-full bg-[#ff6b72]"
                    ></span
                    ><span
                      class="h-[9px] w-[9px] rounded-full bg-[#ffcd56]"
                    ></span
                    ><span
                      class="h-[9px] w-[9px] rounded-full bg-[#30d38b]"
                    ></span>
                  </div>
                  <span class="font-mono text-[10px] text-slate-500"
                    >csu-student-workspace / terminal</span
                  ><AppIcon name="terminal" :size="17" />
                </div>
                <div class="space-y-6 p-6 sm:p-8">
                  <div class="font-mono text-xs leading-7">
                    <span class="text-emerald-400">student@csu</span
                    ><span class="text-slate-500"> : </span
                    ><span class="text-cyan-300">~/myschool</span
                    ><span class="text-slate-400"> $ status</span>
                    <div class="mt-3 text-slate-400">
                      Initializing student experience...
                    </div>
                    <div class="text-emerald-400">
                      ✓ Welcome to CSU Landing Page
                    </div>
                    <div class="text-emerald-400">
                      ✓ Student profile... loaded!
                    </div>
                    <div class="text-amber-300">
                      → Hi Khizha, BSIT 3 Caraga State University<span
                        class="blink-cursor"
                        >__</span
                      >
                    </div>
                  </div>
                  <div class="grid grid-cols-2 gap-3">
                    <div
                      class="rounded-xl border border-emerald-400/15 bg-emerald-400/[.07] p-4"
                    >
                      <div
                        class="text-[10px] uppercase tracking-[.16em] text-slate-400"
                      >
                        Department
                      </div>
                      <div
                        class="mt-3 text-3xl font-black tracking-tight text-emerald-300"
                      >
                        CCIS
                      </div>
                      <div class="mt-1 text-[10px] text-slate-400">
                        College community
                      </div>
                    </div>
                    <div
                      class="rounded-xl border border-cyan-300/15 bg-cyan-400/[.055] p-4"
                    >
                      <div
                        class="text-[10px] uppercase tracking-[.16em] text-slate-400"
                      >
                        Student track
                      </div>
                      <div
                        class="mt-3 text-3xl font-black tracking-tight text-cyan-300"
                      >
                        BSIT
                      </div>
                      <div class="mt-1 text-[10px] text-slate-400">
                        Information Technology
                      </div>
                    </div>
                  </div>
                  <div
                    class="flex items-center justify-between border-t border-white/10 pt-4 text-[11px] text-slate-400"
                  >
                    <span class="inline-flex items-center gap-2"
                      ><span class="status-dot"></span> SYSTEM ONLINE</span
                    ><span class="font-mono text-emerald-300"
                      >system loaded... nuxt & tailwind</span
                    >
                  </div>
                </div>
              </div>
              <div
                class="absolute -bottom-5 -left-5 hidden items-center gap-2 rounded-xl border border-white/15 bg-[#142238] px-4 py-3 text-xs font-semibold shadow-xl sm:flex"
              >
                <AppIcon name="spark" :size="17" /> Future-ready interface
              </div>
            </div>
          </div>
          <div class="mt-24 flex flex-wrap items-end justify-between gap-3">
            <div>
              <div class="section-kicker">01 / OUR FOUNDATION</div>
              <h2
                class="mt-3 text-3xl font-black tracking-[-.05em] sm:text-4xl"
              >
                The values that move us.
              </h2>
            </div>
            <span class="text-xs text-slate-500"
              >COMPETENCE · SERVICE · UPRIGHTNESS</span
            >
          </div>
          <div class="mt-7 grid gap-4 md:grid-cols-3">
            <article
              v-for="value in coreValues"
              :key="value.number"
              class="group value-card relative overflow-hidden rounded-2xl border border-white/[.09] bg-[#101c2b]/80 p-6 sm:p-7"
            >
              <div class="flex items-start justify-between">
                <span
                  class="grid h-12 w-12 place-items-center rounded-xl border border-emerald-400/20 bg-emerald-400/10 text-emerald-300"
                  ><AppIcon :name="value.icon" :size="25" /></span
                ><span class="font-mono text-xs text-slate-500">{{
                  value.number
                }}</span>
              </div>
              <h3 class="mt-8 text-[23px] font-extrabold tracking-[-.04em]">
                {{ value.title }}
              </h3>
              <p class="mt-3 min-h-[70px] text-sm leading-7 text-slate-400">
                {{ value.description }}
              </p>
              <div
                class="mt-6 border-t border-white/[.07] pt-4 font-mono text-xs text-emerald-400"
              >
                &gt; {{ value.code }}
              </div>
            </article>
          </div>
          <p class="mt-5 text-[11px] text-slate-500">
            CSU's official core values:
            <a
              class="text-slate-300 underline decoration-white/20 hover:text-emerald-300"
              href="https://www.carsu.edu.ph/about-us/"
              target="_blank"
              rel="noopener noreferrer"
              >Competence, Service, and Uprightness</a
            >.
          </p>
        </div>
      </section>

      <!-- STYLE 02 / University information: editorial composition + timeline -->
      <section
        id="university"
        class="section-panel relative overflow-hidden border-b border-white/[.07] bg-[#0d1828]"
      >
        <div
          class="absolute -right-12 top-16 select-none text-[clamp(11rem,32vw,35rem)] font-black leading-none tracking-[-.13em] text-white/[.025]"
          aria-hidden="true"
        >
          CSU
        </div>
        <div
          class="relative mx-auto max-w-[1310px] px-5 py-[5.5rem] sm:px-8 lg:px-10 lg:py-[7.5rem]"
        >
          <div class="section-kicker">02 / THE UNIVERSITY</div>
          <div class="mt-6 grid gap-12 lg:grid-cols-[.95fr_1.05fr] lg:gap-20">
            <div>
              <h2
                class="max-w-xl text-[clamp(2.7rem,5.8vw,5.7rem)] font-black leading-[1.04] tracking-[-.075em]"
              >
                Rooted in<br /><span class="text-emerald-400">purpose.</span
                ><br />Built for<br />tomorrow.
              </h2>
              <div class="mt-8 h-[3px] w-20 bg-emerald-400"></div>
              <p class="mt-8 max-w-xl text-[16px] leading-8 text-[#a8b8c7]">
                CSU is a globally engaged institution, advancing innovation and
                excellence in science, engineering, and the arts, dedicated to
                cultivating problem-solvers for a sustainable future.
              </p>
              <a
                class="mt-7 inline-flex items-center gap-2 text-sm font-semibold text-emerald-300 hover:text-emerald-200"
                target="_blank"
                rel="noopener noreferrer"
                href="https://www.carsu.edu.ph/about-us/"
                >Read official university profile <AppIcon name="up" :size="16"
              /></a>
            </div>
            <div class="flex flex-col gap-4">
              <div
                class="rounded-[22px] border border-white/10 bg-[#132338]/90 p-7 sm:p-8"
              >
                <div class="flex items-center justify-between">
                  <span
                    class="text-[11px] font-bold tracking-[.19em] text-emerald-400"
                    >MISSION DRIVEN</span
                  ><AppIcon name="globe" :size="22" />
                </div>
                <h3 class="mt-5 text-2xl font-extrabold tracking-[-.04em]">
                  Creating futures. Empowering communities.
                </h3>
                <p class="mt-4 text-sm leading-8 text-slate-400">
                  As a transformative university, CSU is a responsible steward
                  of problem-solvers and value creators who are driven to create
                  a sustainable future for the region, the nation,and beyond.
                </p>
              </div>
              <div class="grid gap-4 sm:grid-cols-2">
                <div
                  class="rounded-[20px] border border-cyan-400/15 bg-cyan-400/[.04] p-6"
                >
                  <span
                    class="text-[11px] font-bold tracking-[.17em] text-cyan-300"
                    >VISION</span
                  >
                  <p class="mt-3 text-sm leading-7 text-slate-300">
                    A socially engaged, digital, innovative, and entrepreneurial
                    university.
                  </p>
                </div>
                <div
                  class="rounded-[20px] border border-amber-400/15 bg-amber-400/[.04] p-6"
                >
                  <span
                    class="text-[11px] font-bold tracking-[.17em] text-amber-300"
                    >LOCATION</span
                  >
                  <p class="mt-3 text-sm leading-7 text-slate-300">
                    Main Campus<br />Ampayon, Butuan City<br />Agusan del Norte
                  </p>
                </div>
              </div>
            </div>
          </div>
          <div
            class="mt-15 grid gap-4 border-t border-white/10 pt-8 md:grid-cols-3"
          >
            <div class="timeline-item">
              <span>1946</span><strong>School foundations</strong>
              <p>
                Agusan Agricultural High School began operating in the reserved
                campus area.
              </p>
            </div>
            <div class="timeline-item">
              <span>2010</span><strong>University established</strong>
              <p>Republic Act No. 9854 established Caraga State University.</p>
            </div>
            <div class="timeline-item">
              <span>TODAY</span><strong>Innovation & learning</strong>
              <p>
                CSU continues providing education, research, and community
                engagement.
              </p>
            </div>
          </div>
          <p class="mt-5 text-[11px] text-slate-500">
            Historical overview adapted from
            <a
              href="https://www.carsu.edu.ph/history/"
              class="underline decoration-white/25 hover:text-emerald-300"
              target="_blank"
              rel="noopener noreferrer"
              >CSU's official history</a>
          </p>
        </div>
      </section>

      <!-- STYLE 03 / Announcements: searchable information dashboard -->
      <section
        id="announcements"
        class="section-panel announcement-pattern relative border-b border-white/[.07] bg-[#08121d]"
      >
        <div
          class="mx-auto max-w-[1310px] px-5 py-[5.5rem] sm:px-8 lg:px-10 lg:py-[7.5rem]"
        >
          <div class="flex flex-wrap items-end justify-between gap-6">
            <div>
              <div class="section-kicker">03 / CAMPUS BULLETIN</div>
              <h2
                class="mt-4 text-[clamp(2.7rem,5vw,5rem)] font-black tracking-[-.065em]"
              >
                Stay in the <span class="text-emerald-400">loop.</span>
              </h2>
            </div>
            <a
              class="action-outline"
              href="https://www.carsu.edu.ph/"
              target="_blank"
              rel="noopener noreferrer"
              >Official CSU news <AppIcon name="up" :size="16"
            /></a>
          </div>
          <div
            class="mt-8 flex flex-col justify-between gap-4 lg:flex-row lg:items-center"
          >
            <div
              class="flex flex-wrap gap-2"
              role="group"
              aria-label="Filter announcement categories"
            >
              <button
                v-for="filter in filters"
                :key="filter"
                type="button"
                class="filter-pill"
                :class="{ 'filter-on': selectedFilter === filter }"
                :aria-pressed="selectedFilter === filter"
                @click="selectedFilter = filter"
              >
                {{ filter }}
              </button>
            </div>
            <div class="relative w-full max-w-md">
              <AppIcon
                name="search"
                :size="16"
                class="absolute left-4 top-3.5 text-slate-400"
              /><input
                v-model="searchTerm"
                aria-label="Search demonstration announcements"
                type="search"
                placeholder="Search announcements..."
                class="w-full rounded-xl border border-white/15 bg-white/[.04] py-3 pl-11 pr-4 text-sm text-white outline-none placeholder:text-slate-500 focus:border-emerald-400/70"
              />
            </div>
          </div>
          <div
            v-if="filteredNotices.length"
            class="mt-6 grid gap-4 md:grid-cols-2"
          >
            <a
              v-for="notice in filteredNotices"
              :key="notice.id"
              :href="notice.href"
              target="_blank"
              rel="noopener noreferrer"
              class="announcement-card group flex min-h-[230px] flex-col justify-between rounded-[21px] border border-white/10 bg-[#122033]/90 p-6 transition duration-200 hover:-translate-y-1 hover:border-emerald-400/40 hover:bg-[#15273a] sm:p-7"
              ><div class="flex items-center justify-between">
                <span
                  class="font-mono text-[10px] tracking-[.12em] text-emerald-300"
                  >{{ notice.eyebrow }}</span
                ><span
                  class="grid h-10 w-10 place-items-center rounded-xl border border-white/10 bg-white/[.05] text-emerald-300"
                  ><AppIcon :name="notice.icon" :size="19"
                /></span>
              </div>
              <div class="mt-6">
                <h3 class="text-[21px] font-extrabold tracking-[-.04em]">
                  {{ notice.title }}
                </h3>
                <p class="mt-2 max-w-lg text-sm leading-7 text-slate-400">
                  {{ notice.summary }}
                </p>
              </div>
              <div
                class="mt-6 flex items-center justify-between border-t border-white/10 pt-4 text-xs font-semibold text-slate-400"
              >
                <span>{{ notice.type }} · Illustrative</span
                ><span class="flex items-center gap-2 text-emerald-300"
                  >Open official source <AppIcon name="up" :size="15"
                /></span></div
            ></a>
          </div>
          <div
            v-else
            class="mt-6 rounded-xl border border-dashed border-white/20 p-12 text-center text-sm text-slate-400"
          >
            No example announcements match that filter. Try another category or
            search term.
          </div>
        </div>
      </section>

      <!-- STYLE 04 / Profile: identity card meets developer console -->
      <section
        id="profile"
        class="section-panel relative overflow-hidden bg-[#0b1020]"
      >
        <div
          class="pointer-events-none absolute bottom-0 right-0 h-[620px] w-[620px] rounded-full bg-cyan-500/[.055] blur-[125px]"
        ></div>
        <div
          class="relative mx-auto max-w-[1310px] px-5 py-[5.5rem] sm:px-8 lg:px-10 lg:py-[7.5rem]"
        >
          <div class="section-kicker">04 / STUDENT IDENTITY</div>
          <h2
            class="mt-4 text-[clamp(2.7rem,5vw,5rem)] font-black tracking-[-.065em]"
          >
            The person behind<br /><span class="text-cyan-300"
              >the progress.</span
            >
          </h2>
          <p class="mt-5 max-w-2xl text-[15px] leading-7 text-slate-400">
            A personal space for identity, learning goals, and student
            shortcuts—designed as a demo, not connected to real enrollment
            records.
          </p>
          <div class="mt-10 grid gap-5 lg:grid-cols-[1fr_.85fr]">
            <div
              class="overflow-hidden rounded-[25px] border border-white/10 bg-[#142137] shadow-[0_24px_70px_rgba(0,0,0,.25)]"
            >
              <div class="profile-top h-28 border-b border-white/10"></div>
              <div class="px-6 pb-8 sm:px-9">
                <div
                  class="-mt-11 flex flex-wrap items-end justify-between gap-3"
                >
                  <div
                    class="h-24 w-24 shrink-0 overflow-hidden rounded-[22px] border-[5px] border-[#142137] bg-emerald-400/10"
                  >
                    <img
                      :src="duquesaPhoto"
                      alt="Khizha Duquesa profile picture"
                      class="h-full w-full object-cover object-center"
                      width="96"
                      height="96"
                    />
                  </div>

                  <span
                    class="mb-2 rounded-full border border-emerald-400/20 bg-emerald-400/[.09] px-3 py-1.5 text-[10px] font-bold tracking-[.14em] text-emerald-300"
                    >STUDENT PROFILE</span
                  >
                </div>
                <h3
                  class="mt-5 text-2xl font-black tracking-[-.045em] sm:text-3xl"
                >
                  Khizha Rhizhane C. Duquesa
                </h3>
                <div class="mt-2 text-sm text-slate-400">
                  Finds inspiration in technology, loves building concepts and
                  building growth, and is passionate about learning new things.
                </div>
                <div
                  class="mt-7 grid gap-4 border-t border-white/10 pt-6 sm:grid-cols-2"
                >
                  <div>
                    <span class="profile-label">ACADEMIC SECTION</span
                    ><strong class="mt-2 block text-lg text-white"
                      >ITE 18 - AIEM1</strong
                    >
                  </div>
                  <div>
                    <span class="profile-label">COLLEGE</span
                    ><strong class="mt-2 block text-lg text-white"
                      >CCIS Department</strong
                    >
                  </div>
                  <div>
                    <span class="profile-label">CAMPUS</span
                    ><strong class="mt-2 block text-base text-white"
                      >Caraga State University – Main Campus</strong
                    >
                  </div>
                  <div>
                    <span class="profile-label">COURSE</span
                    ><strong class="mt-2 block text-base text-white"
                      >BSIT - 3</strong
                    >
                  </div>
                </div>
              </div>
            </div>
            <div class="flex items-start">
              <div
                class="h-full w-full rounded-[23px] border border-white/10 bg-[#111b2d] p-7"
              >
                <div class="flex items-center gap-3">
                  <span
                    class="grid h-10 w-10 place-items-center rounded-xl bg-cyan-400/10 text-cyan-300"
                    ><AppIcon name="terminal" :size="21" /></span
                  ><span class="text-sm font-bold">Current project stack</span>
                </div>
                <div class="mt-6 flex flex-wrap gap-2">
                  <span
                    v-for="tech in [
                      'Nuxt',
                      'Vue',
                      'Tailwind CSS',
                      'Responsive UI',
                      'Frontend',
                    ]"
                    :key="tech"
                    class="rounded-lg border border-cyan-400/15 bg-cyan-400/[.055] px-3 py-2 font-mono text-[11px] text-cyan-200"
                    >{{ tech }}</span
                  >
                </div>
                <div
                  class="mt-7 border-t border-white/10 pt-5 font-mono text-xs leading-7"
                >
                  <div class="text-slate-500">// project objective</div>
                  <div class="text-emerald-400">
                    const goal =
                    <span class="text-amber-200"
                      >'ITE 18 Submission for Laboratory Activity'</span
                    >
                  </div>
                  <div class="text-slate-300">
                    status: <span class="text-cyan-300">'completed...'</span>
                  </div>
                </div>
              </div>
            </div>
          </div>
          <div
            class="mt-11 flex flex-wrap justify-between gap-4 border-t border-white/10 pt-6 text-xs text-slate-500"
          >
            <p>MySchool - Caraga State Univesity</p>
            <button
              type="button"
              class="flex items-center gap-2 font-bold text-emerald-300 hover:text-white"
              @click="jumpTo('values')"
            >
              Back to top <AppIcon name="arrow" :size="15" />
            </button>
          </div>
        </div>
      </section>
    </main>

    <!-- A true persistent footer: in app's flex layout, not an overlapping fixed overlay. -->
    <footer
      class="site-footer z-40 flex h-11 shrink-0 items-center border-t border-white/10 bg-[#080e19]"
    >
      <div
        class="mx-auto flex w-full max-w-[1450px] items-center justify-between gap-2 px-5 text-[10px] text-slate-400 sm:px-8 lg:px-12"
      >
        <span class="truncate"
          >© 2026 · Caraga State Univesity
          <span class="hidden sm:inline">. All Rights Reserved.</span></span
        ><span
          class="hidden items-center gap-2 font-mono tracking-[.10em] text-emerald-300 sm:flex"
          ><span class="status-dot"></span> NIGHT TECH / ONLINE</span
        ><span class="truncate text-right"
          >College of Computing and Information Sciences</span
        >
      </div>
    </footer>
  </div>
</template>
