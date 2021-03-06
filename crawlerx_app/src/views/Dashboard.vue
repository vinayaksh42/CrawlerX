<template>
    <div
            id="demo"
            :class="[{'collapsed' : collapsed}, {'onmobile' : isOnMobile}]"
    >
        <div class="">
            <md-toolbar md-get-palette-color="red" md-elevation="1">
                <h3 class="md-title" style="flex: 1"><b>CrawlerX</b> - Open-Source Crawling Platform</h3>
                <b-button v-b-modal.modal-new-project variant="outline-secondary">Create a Project</b-button>
                <b-button v-b-modal.modal-new-job variant="outline-secondary" class="ml-2">Schedule a Job</b-button>
                <b-button variant="primary" class="ml-2" @click="logout">Logout</b-button>
            </md-toolbar>
            <sidebar-menu
                    :menu="menu"
                    :collapsed="collapsed"
                    :theme="selectedTheme"
                    :show-one-child="true"
                    @toggle-collapse="onToggleCollapse"
                    @item-click="onItemClick"
            />
            <router-view/>
            <div
                    v-if="isOnMobile && !collapsed"
                    class="sidebar-overlay"
                    @click="collapsed = true"></div>
        </div>

        <b-modal id="modal-new-project" size="lg" title="Create a new project">
            <form ref="form" @submit.stop.prevent="handleSubmit">
                <b-form-group
                        :state="nameState"
                        label="Project Name:"
                        label-for="name-input"
                        invalid-feedback="Name is required"
                >
                    <b-form-input
                            id="name-input"
                            v-model="name"
                            :state="nameState"
                            required
                    ></b-form-input>
                </b-form-group>
            </form>
        </b-modal>

        <b-modal id="modal-new-job" size="lg" title="Create a new crawl job">
            <form ref="form" @submit.stop.prevent="handleSubmit">
                <b-form-group
                        :state="nameState"
                        label="Job Name:"
                        label-for="name-input"
                        invalid-feedback="Name is required"
                >
                    <b-form-input
                            id="name-input"
                            v-model="name"
                            :state="nameState"
                            required
                    ></b-form-input>
                </b-form-group>
                <b-form-group
                        label="Project:"
                        label-for="project-input"
                >
                    <b-form-select v-model="selected" :options="projectOptions" />
                </b-form-group>
                <b-form-group
                        :state="nameState"
                        label="Crawling Type:"
                        label-for="crawl-type-input"
                >
                    <b-form-select v-model="selected" :options="crawlOptions" />
                </b-form-group>
                <b-form-group
                        label="URLs:"
                        :state="urlsState"
                        label-for="url-input"
                >
                    <b-form-tags
                            input-id="url-tags"
                            :input-attrs="{ 'aria-describedby': 'tags-remove-on-delete-help' }"
                            v-model="value"
                            separator=" "
                            placeholder="Enter new urls separated by space"
                            remove-on-delete
                            no-add-on-enter
                            :state="urlsState"
                            class="mb-2"
                    ></b-form-tags>
                    <b-form-text id="tags-remove-on-delete-help">
                        Press <kbd>Backspace</kbd> to remove the last url entered
                    </b-form-text>
                </b-form-group>
            </form>
        </b-modal>
    </div>
</template>

<script>
    const separator = {
        template: `<hr style="border-color: rgba(0, 0, 0, 0.1); margin: 20px;">`
    };

    // @ is an alias to /src
    // import HelloWorld from '@/components/HelloWorld.vue'
    import firebase from 'firebase'

    export default {
        name: 'Home',
        components: {
            // HelloWorld,
        },
        computed: {
            nameState() {
                return this.name.length > 0 ? true : false
            },
            urlsState() {
                return this.value.length > 0 ? true : false
            }
        },
        data() {
            return {
                menu: [
                    {
                        header: true,
                        title: 'Configurations',
                        hiddenOnCollapse: true
                    },
                    {
                        href: '/dashboard',
                        title: 'Dashboard',
                        icon: 'fa fa-download'
                    },
                    {
                        header: true,
                        title: 'Features',
                        hiddenOnCollapse: true
                    },
                    {
                        href: '/dashboard/projects',
                        title: 'Projects',
                        icon: 'fa fa-cogs'
                    },
                    {
                        href: '/jobs',
                        title: 'Jobs',
                        icon: 'fa fa-bell'
                    },
                    {
                        component: separator
                    },
                    {
                        header: true,
                        title: 'Analysis',
                        hiddenOnCollapse: true
                    },
                    {
                        href: '/analysis',
                        title: 'ELK Analysis',
                        icon: 'fa fa-bell'
                    }
                ],
                collapsed: false,
                selectedTheme: 'Default theme',
                isOnMobile: false,
                name: '',
                selected: null,
                projectOptions: [
                    { value: 'a', text: 'This is First option' },
                    { value: 'b', text: 'Selected Option' }
                ],
                crawlOptions: [
                    { value: 'a', text: 'This is First option' },
                    { value: 'b', text: 'Selected Option' }
                ],
                value: []
            }
        },
        mounted() {
            this.onResize()
            window.addEventListener('resize', this.onResize)
        },
        methods: {
            logout: function () {
                firebase.auth().signOut().then(() => {
                    this.$router.replace('login');
                })
            },
            onToggleCollapse(collapsed) {
                this.collapsed = collapsed
            },
            onResize() {
                if (window.innerWidth <= 767) {
                    this.isOnMobile = true
                    this.collapsed = true
                } else {
                    this.isOnMobile = false
                    this.collapsed = false
                }
            }
        }
    }
</script>

<style lang="scss">
    body {
        font-family: 'Source Sans Pro', sans-serif;
        background-color: #f2f4f7;
    }

    #demo {
        padding-left: 350px;
        transition: 0.3s ease;
    }
    #demo.collapsed {
        padding-left: 50px;
    }
    #demo.onmobile {
        padding-left: 50px;
    }

    .sidebar-overlay {
        position: fixed;
        width: 100%;
        height: 100%;
        top: 0;
        left: 0;
        background-color: #000;
        opacity: 0.5;
        z-index: 900;
    }

    /*.demo {*/
        /*padding: 50px;*/
    /*}*/

    .container {
        max-width: 900px;
    }

    pre {
        font-family: Consolas, monospace;
        color: #000;
        background: #fff;
        border-radius: 2px;
        padding: 15px;
        line-height: 1.5;
        overflow: auto;
    }
</style>