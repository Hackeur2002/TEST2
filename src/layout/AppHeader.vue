<template>
    <header class="header-global">
        <base-nav class="navbar-main" transparent type="" effect="light" expand>
            <router-link slot="brand" class="navbar-brand mr-lg-5" to="/">
                LOGO
            </router-link>

            <div class="row" slot="content-header" slot-scope="{closeMenu}">
                <div class="col-6 collapse-brand">
                    <a href="">
                        LOGO
                    </a>
                </div>
                <div class="col-6 collapse-close">
                    <close-button @click="closeMenu"></close-button>
                </div>
            </div>

            <ul class="navbar-nav navbar-nav-hover align-items-lg-center">
                <router-link slot="brand" class="navbar-brand mr-lg-5" to="/evenements">
                    <i class="ni ni-ui-04 d-lg-none"></i>
                    <span class="nav-link-inner--text">Evènements</span>
                </router-link>
                <router-link slot="brand" class="navbar-brand mr-lg-5" to="/contact">
                    <i class="ni ni-ui-04 d-lg-none"></i>
                    <span class="nav-link-inner--text">Contact</span>
                </router-link>
                <router-link slot="brand" class="navbar-brand mr-lg-5" to="/profile">
                    <i class="ni ni-ui-04 d-lg-none"></i>
                    <span class="nav-link-inner--text">Profile</span>
                </router-link>
                
                <!-- <base-dropdown tag="li" class="nav-item">
                    <a slot="title" href="#" class="nav-link" data-toggle="dropdown" role="button">
                        <i class="ni ni-collection d-lg-none"></i>
                        <span class="nav-link-inner--text">Examples</span>
                    </a>
                    <router-link to="/landing" class="dropdown-item">Landing</router-link>
                    <router-link to="/profile" class="dropdown-item">Profile</router-link>
                    <router-link to="/login" class="dropdown-item">Login</router-link>
                    <router-link to="/register" class="dropdown-item">Register</router-link>
                </base-dropdown> -->
            </ul>
        </base-nav>
    </header>
</template>
<script>
import BaseNav from "@/components/BaseNav";
import CloseButton from "@/components/CloseButton";

export default {
    components: {
        BaseNav,
        CloseButton
    }
};
</script>
<style></style>
