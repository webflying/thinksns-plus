<style lang="css" module>
    .container {
        padding: 15px;
    }
    .loadding {
        text-align: center;
        font-size: 42px;
    }
    .loaddingIcon {
        animation-name: "TurnAround";
        animation-duration: 1.4s;
        animation-timing-function: linear;
        animation-iteration-count: infinite;
    }
</style>

<template>
    <div :class="$style.container">
        <div class="panel panel-default">
          <div class="panel-heading">
            <router-link type="button" class="btn btn-primary btn-sm" to="/ad">返回</router-link>
          </div>
          <!-- 广告列表 -->
          <div class="panel-body form-horizontal">
              <div class="col-md-7 col-md-offset-2">
                <!-- 标题 -->
                <div class="form-group">
                  <label class="col-md-2 control-label">标题</label>
                  <div class="col-md-7">
                    <input type="text" class="form-control" v-model="ad.title" placeholder="标题">
                  </div>
                </div>
                <!-- 广告位置 -->
                <div class="form-group">
                  <label class="col-md-2 control-label">位置</label>
                  <div class="col-md-7">
                    <select class="form-control" v-model="ad.space_id" @change="spaceChang">
                      <option v-for="space in spaces" :value="space.id">{{ space.alias }}</option>
                    </select>
                  </div>
                </div>
                <!-- 启动广告时长 -->
                <div class="form-group" v-show="ad.space_id === 1">
                  <label class="col-md-2 control-label">广告时长</label>
                  <div class="col-md-7">
                    <input type="number" value="" class="form-control" placeholder="广告持续时长，不能小于1" v-model="ad.data.duration">
                  </div>
                </div>
                <!-- 类型 -->
                <div class="form-group" v-show="ad.space_id">
                  <label class="col-md-2 control-label">类型</label>
                  <div class="col-md-7">
                    <select class="form-control" v-model="ad.type" @change="typeChang">
                      <option v-for="type in types" :value="type">{{ type }}</option>
                    </select>
                  </div>
                </div>
                <div v-show="ad.type">
                  <template>
                    <div v-for="(item, key) in format">
                      <!-- 头像 -->
                      <div class="form-group" v-if="key=='avatar'">
                        <label class="col-md-2 control-label">{{ (item.split('|'))[0] }}</label>
                        <div class="col-md-7">
                          <div class="input-group">
                            <input type="url" class="form-control" placeholder="头像链接" v-model="ad.data.avatar">
                            <span class="input-group-btn">
                              <button class="btn btn-default" @click="triggerUpload(key)" id="">上传</button>
                              <input type="file" class="hide" :class="key+'-input'" @change="uploadAttachment(key)">
                            </span>
                          </div>
                        </div>
                      </div>
                      <!-- 广告名称 -->
                      <div class="form-group" v-else-if="key=='name'">
                        <label class="col-md-2 control-label">{{ (item.split('|'))[0] }}</label>
                        <div class="col-md-7">
                          <input type="text" class="form-control" v-model="ad.data.name" placeholder="用户名">
                        </div>
                      </div>
                      <!-- 广告内容 -->
                      <div class="form-group" v-else-if="key=='content'">
                        <label class="col-md-2 control-label">{{ (item.split('|'))[0] }}</label>
                        <div class="col-md-7">
                          <textarea class="form-control" v-model="ad.data.content" placeholder="内容"></textarea>
                        </div>
                      </div>
                      <!-- 投放时间 -->
                      <div class="form-group" v-else-if="key=='time'">
                        <label class="col-md-2 control-label">{{ (item.split('|'))[0] }}</label>
                        <div class="col-md-7">
                           <input type="datetime-local" class="form-control" v-model="ad.data.time">
                        </div>
                      </div>
                      <!-- 广告来源 -->
                      <div class="form-group" v-else-if="key=='from'">
                        <label class="col-md-2 control-label">{{ (item.split('|'))[0] }}</label>
                        <div class="col-md-7">
                           <input type="text" class="form-control" v-model="ad.data.from" placeholder="广告来源">
                        </div>
                      </div>
                      <!-- 广告图片 -->
                      <div class="form-group" v-if="key=='image'">
                        <label class="col-md-2 control-label">{{ (item.split('|'))[0] }}</label>
                        <div class="col-md-7">
                          <div class="input-group">
                            <input type="url" class="form-control" placeholder="图片链接" v-model="ad.data.image">
                            <span class="input-group-btn">
                              <button class="btn btn-default" @click="triggerUpload(key)">上传</button>
                              <input type="file" class="hide" :class="key+'-input'" @change="uploadAttachment(key)">
                            </span>
                          </div>
                        </div>
                      </div>
                      <!-- 广告链接 -->
                      <div class="form-group" v-else-if="key=='link'">
                        <label class="col-md-2 control-label">{{ (item.split('|'))[0] }}</label>
                        <div class="col-md-7">
                          <input type="text" class="form-control" v-model="ad.data.link" placeholder="广告链接">
                        </div>
                      </div>
                      <!-- 广告标题 -->
                      <div class="form-group" v-else-if="key=='title'">
                        <label class="col-md-2 control-label">{{ (item.split('|'))[0] }}</label>
                        <div class="col-md-7">
                          <input type="text" class="form-control" v-model="ad.data.title" placeholder="标题">
                        </div>
                      </div>
                    </div>
                  </template>
                </div>
                <!-- 广告排序 -->
                <div class="form-group">
                  <label class="col-md-2 control-label">排序</label>
                  <div class="col-md-7">
                    <input type="number" value="0" class="form-control" v-model="ad.sort">
                  </div>
                </div>
                <div class="form-group">
                  <label class="col-md-2 control-label"></label>
                  <div class="col-md-7">
                    <button class="btn btn-primary btn-sm" @click="updateAd">确定</button>
                  </div>
                    <div class="col-md-3">
                       <span class="text-success"  v-show="message.success">{{ message.success }}</span>
                       <span class="text-danger" v-show="message.error">{{ message.error }}</span>
                    </div>
                </div>
              </div>
          </div>
        </div>
    </div>
</template>
<script>
import request, { createRequestURI } from '../../util/request';
import plusMessageBundle from 'plus-message-bundle';
const AddAdComponent = {

    data: () => ({

      loadding: true,
      
      ad: {
        title: null,
        space_id: null,
        type: null,
        sort: 0,
        data: {
          image: null,
          title: null,
          link: null,
          from: null,
          time: null,
          avatar: null,
          name: null,
          duration: 0
        }
      },

      spaces: {},

      types: [],

      format: {},

      message: {
        success: null,
        error: null,
      }    
    }),
    
    methods: {

      getAdSpaces () {
        request.get(
          createRequestURI('ads/spaces'),
          { validateStatus: status => status === 200 }
        ).then(response => {
          this.spaces = response.data;
        }).catch(({ response: { data: { errors = ['加载认证类型失败'] } = {} } = {} }) => {
        });
      },

      getAd () {
        let id = this.$route.params.id;
        request.get(
          createRequestURI(`ads/${id}`),
          { validateStatus: status => status === 200 }
        ).then(response => {

          let data = response.data;

          if (data.data.hasOwnProperty('time')) {
            data.data.time = this.isoTime(data.data.time);
          }

          this.ad = data;

          this.spaceChang();

          this.typeChang();

        }).catch(({ response: { data: { errors = ['加载认证类型失败'] } = {} } = {} }) => {
        });
      },

      updateAd () {

        this.hiddenMessage();

        let id = this.$route.params.id;

        request.put(
          createRequestURI(`ads/${id}`),
          { ...this.ad },
          { validateStatus: status => status === 201 }
        ).then(({ data: { message: [ message ] = [] } }) => {
            this.message.success = message;
        }).catch(({ response: { data = {} } = {} }) => {
            const Message = plusMessageBundle(data);
            this.message.error = Message.getMessage();
        });
      },

      triggerUpload (key) {
        $('.' + key + '-input').click();
      },

      uploadAttachment (type) {
        let e = window.event || arguments[0];
        var that = this;
        let file = e.target.files[0]; 
        let param = new FormData();
        param.append('file', file);
        // 设置请求头
        let config = {
          headers: { 
            'Content-Type': 'multipart/form-data',
            'Authorization': 'Bearer ' + window.TS.token 
          }
        };
        let reader = new FileReader(); 
        reader.readAsDataURL(file); 
        reader.onload = function(e) {
         request.post('/api/v2/files', param, config)
          .then((response) => {
              const { id: id, message: [message] = [] } = response.data;
              let origin = window.location.origin;
              let fileUrl = origin + '/api/v2/files/' + id;
              
              console.log(that.ad.data);

              if (type == 'image') {
                that.ad.data.image = fileUrl;
              } else if(type == 'avatar') {
                that.ad.data.avatar = fileUrl;
              }
          }).catch((error) => {
             console.log(this.ad);
              console.log(error);
          });
        }
      },

      spaceChang () {
        this.setType();
      },

      typeChang () {
        this.setFormat();
      },

      setType () {
        let spaces = this.spaces;
        for (let i=0; i<spaces.length; i++) {
          if (spaces[i].id == this.ad.space_id) {
            this.types = spaces[i].allow_type.split(',');
          }
        }
      },

      setFormat () {
        let spaces = this.spaces;
        for (let i=0; i<spaces.length; i++) {
          if (spaces[i].id == this.ad.space_id) {
            this.format = spaces[i].format[this.ad.type];
          }
        }
      },

      hiddenMessage () {
        this.message.success = this.message.error = null;
      },

      isoTime (time) {
        let date = new Date(time).toISOString();
        return date.substring(0, date.lastIndexOf(':'));
      }

    },

    created () {
      this.getAdSpaces();
      this.getAd();
    },
};

export default AddAdComponent;
</script>
