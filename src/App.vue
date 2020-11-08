<template>
    <div id="app">
        <Calendar></Calendar>
    </div>
</template>

<script>
    import Calendar from "@/views/Calendar";

    export default {
        name: 'app',
        components: {
            Calendar: Calendar
        }
    }
</script>

<style>
    html, body {
        width: 100%;
        height: 100%;
        margin: 0;
    }

    body{
        background:#FAFAFA;
    }

    #app {
        width: 100%;
        height: 100%;
        display: flex;
        align-items: center;
        justify-content: center;
    }
</style>
