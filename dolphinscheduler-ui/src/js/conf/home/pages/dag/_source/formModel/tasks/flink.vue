/*
 * Licensed to the Apache Software Foundation (ASF) under one or more
 * contributor license agreements.  See the NOTICE file distributed with
 * this work for additional information regarding copyright ownership.
 * The ASF licenses this file to You under the Apache License, Version 2.0
 * (the "License"); you may not use this file except in compliance with
 * the License.  You may obtain a copy of the License at
 *
 *    http://www.apache.org/licenses/LICENSE-2.0
 *
 * Unless required by applicable law or agreed to in writing, software
 * distributed under the License is distributed on an "AS IS" BASIS,
 * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
 * See the License for the specific language governing permissions and
 * limitations under the License.
 */
<template>
  <div class="flink-model">
    <m-list-box>
      <div slot="text">{{$t('Program Type')}}</div>
      <div slot="content">
        <x-select
                style="width: 130px;"
                v-model="programType"
                :disabled="isDetails">
          <x-option
                  v-for="city in programTypeList"
                  :key="city.code"
                  :value="city.code"
                  :label="city.code">
          </x-option>
        </x-select>
      </div>
    </m-list-box>

    <m-list-box v-if="programType !== 'PYTHON'">
      <div slot="text">{{$t('Main class')}}</div>
      <div slot="content">
        <x-input
                :disabled="isDetails"
                type="input"
                v-model="mainClass"
                :placeholder="$t('Please enter main class')"
                autocomplete="off">
        </x-input>
      </div>
    </m-list-box>
    <m-list-box>
      <div slot="text">{{$t('Main jar package')}}</div>
      <div slot="content">
        <x-select
                style="width: 100%;"
                :placeholder="$t('Please enter main jar package')"
                v-model="mainJar"
                filterable
                :disabled="isDetails">
          <x-option
                  v-for="city in mainJarList"
                  :key="city.code"
                  :value="city.code"
                  :label="city.code">
          </x-option>
        </x-select>
      </div>
    </m-list-box>
    <m-list-box>
      <div slot="text">{{$t('Deploy Mode')}}</div>
      <div slot="content">
        <x-radio-group v-model="deployMode">
          <x-radio :label="'cluster'" :disabled="isDetails"></x-radio>
          <x-radio :label="'local'" :disabled="isDetails"></x-radio>
        </x-radio-group>
      </div>
    </m-list-box>
    <div class="list-box-4p">
      <div class="clearfix list">
        <span class="sp1">{{$t('slot')}}</span>
        <span class="sp2">
          <x-input
                  :disabled="isDetails"
                  type="input"
                  v-model="slot"
                  :placeholder="$t('Please enter driver core number')"
                  style="width: 200px;"
                  autocomplete="off">
        </x-input>
        </span>
        <span class="sp1 sp3">{{$t('taskManager')}}</span>
        <span class="sp2">
          <x-input
                  :disabled="isDetails"
                  type="input"
                  v-model="taskManager"
                  :placeholder="$t('Please enter driver memory use')"
                  style="width: 186px;"
                  autocomplete="off">
        </x-input>
        </span>
      </div>
      <div class="clearfix list">
        <span class="sp1">{{$t('jobManagerMemory')}}</span>
        <span class="sp2">
          <x-input
                  :disabled="isDetails"
                  type="input"
                  v-model="jobManagerMemory"
                  :placeholder="$t('Please enter the number of Executor')"
                  style="width: 200px;"
                  autocomplete="off">
        </x-input>
        </span>
        <span class="sp1 sp3">{{$t('taskManagerMemory')}}</span>
        <span class="sp2">
          <x-input
                  :disabled="isDetails"
                  type="input"
                  v-model="taskManagerMemory"
                  :placeholder="$t('Please enter the Executor memory')"
                  style="width: 186px;"
                  autocomplete="off">
        </x-input>
        </span>
      </div>

    </div>
    <m-list-box>
      <div slot="text">{{$t('Command-line parameters')}}</div>
      <div slot="content">
        <x-input
                :autosize="{minRows:2}"
                :disabled="isDetails"
                type="textarea"
                v-model="mainArgs"
                :placeholder="$t('Please enter Command-line parameters')"
                autocomplete="off">
        </x-input>
      </div>
    </m-list-box>
    <m-list-box>
      <div slot="text">{{$t('Other parameters')}}</div>
      <div slot="content">
        <x-input
                :disabled="isDetails"
                :autosize="{minRows:2}"
                type="textarea"
                v-model="others"
                :placeholder="$t('Please enter other parameters')">
        </x-input>
      </div>
    </m-list-box>
    <m-list-box>
      <div slot="text">{{$t('Resources')}}</div>
      <div slot="content">
        <m-resources
                ref="refResources"
                @on-resourcesData="_onResourcesData"
                :resource-list="resourceList">
        </m-resources>
      </div>
    </m-list-box>
    <m-list-box>
      <div slot="text">{{$t('Custom Parameters')}}</div>
      <div slot="content">
        <m-local-params
                ref="refLocalParams"
                @on-local-params="_onLocalParams"
                :udp-list="localParams"
                :hide="false">
        </m-local-params>
      </div>
    </m-list-box>
  </div>
</template>
<script>
  import _ from 'lodash'
  import i18n from '@/module/i18n'
  import mLocalParams from './_source/localParams'
  import mListBox from './_source/listBox'
  import mResources from './_source/resources'
  import disabledState from '@/module/mixin/disabledState'

  export default {
    name: 'flink',
    data () {
      return {
        // Main function class
        mainClass: '',
        // Master jar package
        mainJar: null,
        // Master jar package(List)
        mainJarList: [],
        // Deployment method
        deployMode: 'cluster',
        // Resource(list)
        resourceList: [],
        // Custom function
        localParams: [],
        // Driver Number of cores
        slot: 1,
        // Driver Number of memory
        taskManager: '2',
        // Executor Number
        jobManagerMemory: '1G',
        // Executor Number of memory
        taskManagerMemory: '2G',
        // Executor Number of cores
        executorCores: 2,
        // Command line argument
        mainArgs: '',
        // Other parameters
        others: '',
        // Program type
        programType: 'SCALA',
        // Program type(List)
        programTypeList: [{ code: 'JAVA' }, { code: 'SCALA' }, { code: 'PYTHON' }]
      }
    },
    props: {
      backfillItem: Object
    },
    mixins: [disabledState],
    methods: {
      /**
       * return localParams
       */
      _onLocalParams (a) {
        this.localParams = a
      },
      /**
       * return resourceList
       */
      _onResourcesData (a) {
        this.resourceList = a
      },
      /**
       * verification
       */
      _verification () {
        if (this.programType !== 'PYTHON' && !this.mainClass) {
          this.$message.warning(`${i18n.$t('Please enter main class')}`)
          return false
        }


        if (!this.mainJar) {
          this.$message.warning(`${i18n.$t('Please enter main jar package')}`)
          return false
        }

        if (!this.jobManagerMemory) {
          this.$message.warning(`${i18n.$t('Please enter the number of Executor')}`)
          return false
        }

        if (!Number.isInteger(parseInt(this.jobManagerMemory))) {
          this.$message.warning(`${i18n.$t('The number of Executors should be a positive integer')}`)
          return false
        }

        if (!this.taskManagerMemory) {
          this.$message.warning(`${i18n.$t('Please enter the Executor memory')}`)
          return false
        }

        if (!this.taskManagerMemory) {
          this.$message.warning(`${i18n.$t('Please enter the Executor memory')}`)
          return false
        }

        if (!_.isNumber(parseInt(this.taskManagerMemory))) {
          this.$message.warning(`${i18n.$t('Memory should be a positive integer')}`)
          return false
        }

        if (!this.executorCores) {
          this.$message.warning(`${i18n.$t('Please enter ExecutorPlease enter Executor core number')}`)
          return false
        }

        if (!Number.isInteger(parseInt(this.executorCores))) {
          this.$message.warning(`${i18n.$t('Core number should be positive integer')}`)
          return false
        }

        if (!this.$refs.refResources._verifResources()) {
          return false
        }

        // localParams Subcomponent verification
        if (!this.$refs.refLocalParams._verifProp()) {
          return false
        }

        // storage
        this.$emit('on-params', {
          mainClass: this.mainClass,
          mainJar: {
            res: this.mainJar
          },
          deployMode: this.deployMode,
          resourceList: this.resourceList,
          localParams: this.localParams,
          slot: this.slot,
          taskManager: this.taskManager,
          jobManagerMemory: this.jobManagerMemory,
          taskManagerMemory: this.taskManagerMemory,
          executorCores: this.executorCores,
          mainArgs: this.mainArgs,
          others: this.others,
          programType: this.programType
        })
        return true
      },
      /**
       * get resources list
       */
      _getResourcesList () {
        return new Promise((resolve, reject) => {
          let isJar = (alias) => {
            return alias.substring(alias.lastIndexOf('.') + 1, alias.length) !== 'jar'
          }
          this.mainJarList = _.map(_.cloneDeep(this.store.state.dag.resourcesListS), v => {
            return {
              id: v.id,
              code: v.alias,
              disabled: isJar(v.alias)
            }
          })
          resolve()
        })
      }
    },
    watch: {
      // Listening type
      programType (type) {
        if (type === 'PYTHON') {
          this.mainClass = ''
        }
      }
    },
    created () {
      this._getResourcesList().then(() => {
        let o = this.backfillItem

        // Non-null objects represent backfill
        if (!_.isEmpty(o)) {
          this.mainClass = o.params.mainClass || ''
          this.mainJar = o.params.mainJar.res || ''
          this.deployMode = o.params.deployMode || ''
          this.slot = o.params.slot || 1
          this.taskManager = o.params.taskManager || '2'
          this.jobManagerMemory = o.params.jobManagerMemory || '1G'
          this.taskManagerMemory = o.params.taskManagerMemory || '2G'

          this.mainArgs = o.params.mainArgs || ''
          this.others = o.params.others
          this.programType = o.params.programType || 'SCALA'

          // backfill resourceList
          let resourceList = o.params.resourceList || []
          if (resourceList.length) {
            this.resourceList = resourceList
          }

          // backfill localParams
          let localParams = o.params.localParams || []
          if (localParams.length) {
            this.localParams = localParams
          }
        }
      })
    },
    mounted () {

    },
    components: { mLocalParams, mListBox, mResources }
  }
</script>

<style lang="scss" rel="stylesheet/scss">
  .flink-model {
    .list-box-4p {
      .list {
        margin-bottom: 14px;
        .sp1 {
          float: left;
          width: 112px;
          text-align: right;
          margin-right: 10px;
          font-size: 14px;
          color: #777;
          display: inline-block;
          padding-top: 6px;
        }
        .sp2 {
          float: left;
          margin-right: 4px;
        }
        .sp3 {
          width: 176px;
        }
      }
    }
  }
</style>
