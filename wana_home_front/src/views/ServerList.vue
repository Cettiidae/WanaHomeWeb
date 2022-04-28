<template>
    <div style="max-width: 90%;width: fit-content;">
        <b-row align-h="between" cols="2">
            <b-col md="auto">
                <p style="font-size: larger; font-weight: bold;">欢迎访问 I Wana Home 查房记录网页</p>
            </b-col>
            <b-col class="text-right" md="auto">
                <b-btn size="sm" variant="outline-info" @click="update()">
                    <b-icon-arrow-clockwise/>
                </b-btn>
            </b-col>
        </b-row>
        <b-row cols="1" cols-md="2">
            <b-col class="text-center">
                <iframe src="https://iinformation.info/board/home.html" style="width: 100%; height: 100%; border: transparent; box-shadow: rgba(0, 0, 0, 0.275) 0px 0.125rem 0.25rem !important; border-radius: 0.25rem !important;"></iframe>
            </b-col>
            <b-col>
                <b-col v-for="dc in dc_server" :key="dc.dc_name" class="shadow-sm rounded m-2 p-2">
                    {{ dc.dc_name }}
                    <div>
                        <b-btn class="m-1" v-for="(w_name,w_id) in dc.servers"
                               :key="w_id" size="sm" variant="success" :disabled="!(w_id in servers)"
                               v-b-tooltip.hover.top :title="`最近更新：${time_diff(servers[w_id],false)}前`"
                               @click="$router.push({name:'State',params:{server:w_id}})">
                            {{ w_name }}
                            <b-badge v-if="w_id in servers">{{ time_diff(servers[w_id]) }}</b-badge>
                        </b-btn>
                    </div>
                </b-col>
            </b-col>
        </b-row>
    </div>
</template>

<script lang="ts">
import {Component, Vue} from 'vue-property-decorator';
import {get_server_list} from "@/axios";
import {dc_server} from "@/libs/WardLandDefine";
import {time_diff} from "@/libs/Utils";

@Component({
    components: {
    }
})
export default class ServerList extends Vue {
    servers: { [id: number]: number } = {};
    dc_server = dc_server

    time_diff=time_diff

    update() {
        get_server_list().then(res => {
            if (!res) return;
            this.servers = {};
            for (var server of res)
                this.servers[server.server] = server.last_update;
        });
    }

    mounted() {
        this.update();
    }
}
</script>

<style scoped>
.list_dc {
    background-color: rgba(255, 255, 255, 0.5)
}
</style>
