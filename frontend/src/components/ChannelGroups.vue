<template>
  <group-list :groups="regularChannelGroups" @add="addChannelGroup" @remove="removeChannelGroup" @edit="editChannelGroup"></group-list>
</template>

<script>
  export default {
    components: {
      GroupList: () => import('@/components/GroupList')
    },
    data() {
      return {
        channelGroups: []
      }
    },
    computed: {
      regularChannelGroups() {
        return this.channelGroups.filter(group => group.type === 1)
      }
    },
    methods: {
      getChannelGroupList() {
        return this.$TeamSpeak.execute('channelgrouplist')
      },
      async addChannelGroup(name) {
        try {
          await this.$TeamSpeak.execute('channelgroupadd', {name: name})
        } catch(err) {
          this.$toast.error(err.message, {icon: 'error'})
        }

        try {
          this.channelGroups = await this.getChannelGroupList()
        } catch(err) {
          this.$toast.error(err.message, {icon: 'error'})
        }
      },
      async removeChannelGroup(group, force) {
        try {
          await this.$TeamSpeak.execute('channelgroupdel', {
            cgid: group.cgid,
            force: +force, // unary operator converts true => 1 and false => 0
          })
        } catch(err) {
          this.$toast.error(err.message, {icon: 'error'})
        }

        try {
          this.channelGroups = await this.getChannelGroupList()
        } catch(err) {
          this.$toast.error(err.message, {icon: 'error'})
        }
      },
      editChannelGroup(group) {
        this.$router.push({name: 'channelgroup-edit', params: {cgid: group.cgid}})
      }
    },
    async created() {
      try {
        this.channelGroups = await this.getChannelGroupList()
      } catch(err) {
        this.$toast.error(err.message, {icon: 'error'})
      }
    }
  }

</script>
