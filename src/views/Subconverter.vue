<template>
  <div>
    <el-row style="margin-top: 10px">
      <el-col>
        <el-card>
          <div slot="header">订阅转换
          <svg-icon icon-class="github" style="margin-left: 10px" @click="goToProject" />
          <svg-icon icon-class="telegram" style="margin-left: 10px" @click="gotoTgChannel" />
          <el-container>
            <el-form :model="form" label-width="80px" label-position="left" style="width: 100%">
              <el-form-item label="模式设置:" style="display:none">
                <el-radio v-model="advanced" label="1">基础模式</el-radio>
                <el-radio v-model="advanced" label="2">进阶模式</el-radio>
              </el-form-item>
              <el-form-item label="订阅链接:">
                <el-input
                  v-model="form.sourceSubUrl"
                  type="textarea"
                  rows="3"
                  placeholder="支持各种订阅链接或单节点链接，多个链接每行一个或用 | 分隔"
                />
              </el-form-item>
              <el-form-item label="客户端项:">
                <el-select v-model="form.clientType" style="width: 100%">
                  <el-option v-for="(v, k) in options.clientTypes" :key="k" :label="k" :value="v"></el-option>
                </el-select>
              </el-form-item>
              <el-form-item label="后端地址:">

              <el-select
                  v-model="form.customBackend"
                  allow-create
                  filterable
                  placeholder="可输入自己的后端"
                  style="width: 100%"
                >
                  <el-option v-for="(v, k) in options.customBackend" :key="k" :label="k" :value="v"></el-option>

                </el-select>

              </el-form-item>
              <el-form-item label="短链选择:">
                <el-select 
                  v-model="form.shortType" 
                  allow-create
                  filterable
                  placeholder="可输入其他可用短链API"
                  style="width: 100%"
                >
                  <el-option v-for="(v, k) in options.shortTypes" :key="k" :label="k" :value="v"></el-option>
                </el-select>
              </el-form-item>
              <div v-if="advanced === '2'">
                <el-form-item label="远程配置:">
                  <el-select
                    v-model="form.remoteConfig"
                    allow-create
                    filterable
                    placeholder="请选择"
                    style="width: 100%"
                  >
                    <el-option-group
                      v-for="group in options.remoteConfig"
                      :key="group.label"
                      :label="group.label"
                    >
                      <el-option
                        v-for="item in group.options"
                        :key="item.value"
                        :label="item.label"
                        :value="item.value"
                      ></el-option>
                    </el-option-group>
                    <el-button slot="append" @click="gotoRemoteConfig" icon="el-icon-link">配置示例</el-button>
                  </el-select>
                </el-form-item>
                <el-form-item label-width="0px">
                 <el-collapse>
                    <el-collapse-item>
                    <template slot="title">
                    <el-form-item label="高级功能:" style="width: 100%;">
                     <el-button
                      type="limr"
                      style="width: 100%;"
                      icon="el-icon-more-outline"
                     >点击显示/隐藏</el-button>
                    </el-form-item>    
                    </template>
                    <el-form-item label="包含节点:">
                    <el-input v-model="form.includeRemarks" placeholder="要保留的节点，支持正则" />
                    </el-form-item>
                    <el-form-item label="排除节点:">
                    <el-input v-model="form.excludeRemarks" placeholder="要排除的节点，支持正则" />
                    </el-form-item>
					<el-form-item label="节点命名:">
					<el-input v-model="form.rename" placeholder="举例：`香港@菲律宾``美国@巴西``台湾@俄罗斯`..." />
					</el-form-item>
					<el-form-item label="订阅命名:">
                    <el-input v-model="form.filename" placeholder="返回的订阅文件名" />
                    </el-form-item>
                <el-form-item label-width="0px">
                  <el-row type="flex">
                    <el-col>
                      <el-checkbox v-model="form.nodeList" label="仅输出节点信息" border style="margin-top:5.9px"></el-checkbox>
                    </el-col>
                      <el-popover placement="bottom" v-model="form.extraset">
                      <el-row :gutter="10">
                        <el-col :span="12"><el-checkbox v-model="form.emoji" label="Emoji"></el-checkbox></el-col>
                        <el-col :span="12"><el-checkbox v-model="form.insert" label="网易云"></el-checkbox></el-col>
                      </el-row>
                      <el-row :gutter="10">
                        <el-col :span="12"><el-checkbox v-model="form.udp" label="启用 UDP"></el-checkbox></el-col>
                        <el-col :span="12"><el-checkbox v-model="form.sort" label="排序节点"></el-checkbox></el-col>          
                      </el-row>    
                      <el-row :gutter="10">
                        <el-col :span="12"><el-checkbox v-model="form.tfo" label="启用 TFO"></el-checkbox></el-col>
                        <el-col :span="12"><el-checkbox v-model="form.tpl.surge.doh" label="Surge.DoH"></el-checkbox></el-col>
                      </el-row>
                      <el-row :gutter="10">
                        <el-col :span="12"><el-checkbox v-model="form.appendType" label="插入节点类型"></el-checkbox></el-col>
                        <el-col :span="12"><el-checkbox v-model="form.tpl.clash.doh" label="Clash.DoH"></el-checkbox></el-col>                        
                      </el-row>
                      <el-row :gutter="10">
                        <el-col :span="12"><el-checkbox v-model="form.expand" label="展开规则全文"></el-checkbox></el-col>
                        <el-col :span="12"><el-checkbox v-model="form.new_name" label="Clash新字段名"></el-checkbox></el-col>
                      </el-row>
                      <el-row :gutter="10">
                        <el-col :span="12"><el-checkbox v-model="form.scv" label="跳过证书验证"></el-checkbox></el-col>
                        <el-col :span="12"><el-checkbox v-model="form.fdn" label="过滤不支持节点"></el-checkbox></el-col>
                      </el-row>
                      <el-button slot="reference">更多选项</el-button>
                    </el-popover>
                  </el-row>
                </el-form-item>    
                </el-collapse-item>
                </el-collapse>
                </el-form-item>
              </div>

              <div style="margin-top: 30px"></div>

              <el-divider content-position="center">
                <i class="el-icon-magic-stick"></i>
              </el-divider>

              <el-form-item label="定制订阅:">
                <el-input class="copy-content" disabled v-model="customSubUrl">
                  <el-button
                    slot="append"
                    v-clipboard:copy="customSubUrl"
                    v-clipboard:success="onCopy"
                    ref="copy-btn"
                    icon="el-icon-document-copy"
                  >复制</el-button>
                </el-input>
              </el-form-item>
              <el-form-item label="订阅短链:">
                <el-input class="copy-content" disabled v-model="curtomShortSubUrl">
                  <el-button
                    slot="append"
                    v-clipboard:copy="curtomShortSubUrl"
                    v-clipboard:success="onCopy"
                    ref="copy-btn"
                    icon="el-icon-document-copy"
                  >复制</el-button>
                </el-input>
              </el-form-item>

              <el-form-item label-width="0px" style="margin-top: 40px; text-align: center">
                <el-button
                  style="width: 120px"
                  type="danger"
                  @click="makeUrl"
                  :disabled="form.sourceSubUrl.length === 0"
                >生成订阅链接</el-button>
                <el-button
                  style="width: 120px"
                  type="danger"
                  @click="makeShortUrl"
                  :loading="loading"
                  :disabled="customSubUrl.length === 0"
                >生成短链接</el-button>
                <!-- <el-button style="width: 120px" type="primary" @click="surgeInstall" icon="el-icon-connection">一键导入Surge</el-button> -->
              </el-form-item>

              <el-form-item label-width="0px" style="text-align: center">
                <el-button
                  style="width: 120px"
                  type="primary"
                  @click="dialogUploadConfigVisible = true"
                  icon="el-icon-upload"
                  :loading="loading"
                >上传自定义配置</el-button>
                <el-button
                  style="width: 120px"
                  type="primary"
                  @click="clashInstall"
                  icon="el-icon-connection"
                  :disabled="customSubUrl.length === 0"
                >一键导入Clash</el-button>
              </el-form-item>
            </el-form>
          </el-container>
        </el-card>
      </el-col>
    </el-row>

    <el-dialog
      :visible.sync="dialogUploadConfigVisible"
      :show-close="false"
      :close-on-click-modal="false"
      :close-on-press-escape="false"
      width="80%"
    >
      <div slot="title">
        Remote config upload
        <el-popover trigger="hover" placement="right" style="margin-left: 10px">
          <el-link type="primary" :href="sampleConfig" target="_blank" icon="el-icon-info">参考配置</el-link>
          <i class="el-icon-question" slot="reference"></i>
        </el-popover>
      </div>
      <el-form label-position="left">
        <el-form-item prop="uploadConfig">
          <el-input
            v-model="uploadConfig"
            type="textarea"
            :autosize="{ minRows: 15, maxRows: 15}"
            maxlength="10000"
            show-word-limit
          ></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="uploadConfig = ''; dialogUploadConfigVisible = false">取 消</el-button>
        <el-button
          type="primary"
          @click="confirmUploadConfig"
          :disabled="uploadConfig.length === 0"
        >确 定</el-button>
      </div>
    </el-dialog>
  </div>
</template>
<style>
@media(max-width:460px){
.msgbox {
     width: 80%;
  } 
}
</style>
<script>
const project = process.env.VUE_APP_PROJECT
const remoteConfigSample = process.env.VUE_APP_SUBCONVERTER_REMOTE_CONFIG
const gayhubRelease = process.env.VUE_APP_BACKEND_RELEASE
const defaultBackend = process.env.VUE_APP_SUBCONVERTER_DEFAULT_BACKEND + '/sub?'
const shortUrlBackend = process.env.VUE_APP_MYURLS_DEFAULT_BACKEND + '/short'
const configUploadBackend = process.env.VUE_APP_CONFIG_UPLOAD_BACKEND + '/config/upload'
const tgBotLink = process.env.VUE_APP_BOT_LINK
export default {
  data() {
    return {
      backendVersion: "",
      advanced: "2",

      // 是否为 PC 端
      isPC: true,

      options: {
        clientTypes: {
          Clash: "clash",
          ClashR: "clashr",
          Surge2: "surge&ver=2",
          Surge3: "surge&ver=3",
          Surge4: "surge&ver=4",
          Quantumult: "quan",
          "Quantumult X": "quanx",
          Loon: "loon",
          Mellow: "mellow",
          Surfboard: "surfboard",
          "Shadowsocks(SIP002)": "ss",
          "Shadowsocks Android(SIP008)": "sssub",
          ShadowsocksR: "ssr",
          ShadowsocksD: "ssd",          
          V2Ray: "v2ray",
          Trojan: "trojan",
          "混合订阅（mixed）": "mixed",
          "自动判断客户端": "auto",
        },
        shortTypes: {
         "suo.yt":"https://suo.yt/short",
         "sfw.rip":"https://sfw.rip/short",
         "sub.cm":"https://sub.cm/short",
         "v1.mk":"https://v1.mk/short",
         "d1.mk":"https://d1.mk/short",
         "dlj.tf":"https://dlj.tf/short",
        },
        customBackend: {
          "localhost:25500 本地版": "http://localhost:25500/sub?",
          "azure.jkloli.top(jklolixxs提供)":
            "https://azure.jkloli.top/sub?",
          "sub.lryu4.com(厘米云)":
            "https://sub.lryu4.com/sub?",
          "www.nameless13.com(w8ves)":
            "https://www.nameless13.com/sub?",
          "api.ytoo-163cdn.com(ytoo)":
            "https://api.ytoo-163cdn.com/sub?",
          "sub.maoxiongnet.com(猫熊)":
            "https://sub.maoxiongnet.com/sub?",
          "sub.maoxiongnet.com(ABCloud)":
            "https://api.abc-sub.xyz/sub?",
          "api.weleven11.com(We1eVen)":
            "https://api.weleven11.com/sub?",
          "sub.id9.cc(品云主端)":
            "https://sub.id9.cc/sub?",
          "sub.sub.cm(品云备A)":
            "https://sub.sub.cm/sub?",
	  "Hsichien's后端":
            "https://www.chien.asia/sub?",
          "sub.lpy.pw(品云备B)":
            "https://sub.lpy.pw/sub?",
          "subcon.dlj.tf(subconverter作者提供1)":
            "https://subcon.dlj.tf/sub?",
          "subconverter-web.now.sh(subconverter作者提供2-稳定)":
            "https://subconverter-web.now.sh/sub?",
          "subconverter.herokuapp.com(subconverter作者提供3-稳定)":
            "https://subconverter.herokuapp.com/sub?",
          "api.dler.io(sub作者&lhie1提供-稳定)": "https://api.dler.io/sub?",
          "api.wcc.best(sub-web作者提供-稳定)": "https://api.wcc.best/sub?"
        },
        backendOptions: [
          { value: "http://localhost:25500/sub?" },
          { value: "https://azure.jkloli.top/sub?" },
          { value: "https://sub.lryu4.com/sub?" },
          { value: "https://www.nameless13.com/sub?" },
          { value: "https://api.ytoo-163cdn.com/sub?" },
          { value: "https://sub.maoxiongnet.com/sub?" },
          { value: "https://api.abc-sub.xyz/sub?" },
          { value: "https://api.weleven11.com/sub?" },
          { value: "https://sub.id9.cc/sub?" },
          { value: "https://sub.sub.cm/sub?" },
	  { value: "https://www.chien.asia/sub?" },
          { value: "https://sub.lpy.pw/sub?" },
          { value: "https://subcon.dlj.tf/sub?" },
          { value: "https://subconverter-web.now.sh/sub?" },
          { value: "https://subconverter.herokuapp.com/sub?" },
          { value: "https://api.wcc.best/sub?" }
        ],
        remoteConfig: [
          {
            label: "默认",
            options: [
              {
                label: "不选，由接口提供方提供",
                value: ""
              }
            ]
          },
          {
            label: "自写特供用",
            options: [
              {
                label: "EXFLUX",
                value:
                  "https://gist.github.com/jklolixxs/16964c46bad1821c70fa97109fd6faa2/raw/EXFLUX.ini"
              },
              {
                label: "CNIX",
                value:
                  "https://gist.github.com/jklolixxs/b67b7ae09e89e3716b8885369c7c681a/raw/CNIX.ini"
              },
              {
                label: "Nexitally",
                value:
                  "https://gist.github.com/jklolixxs/f6bbeb6411288e65584a99ae88460fed/raw/Nexitally.ini"
              },
              {
                label: "ImmTelecom",
                value:
                  "https://gist.github.com/jklolixxs/24f4f58bb646ee2c625803eb916fe36d/raw/ImmTelecom.ini"
              },
              {
                label: "AmyTelecom",
                value:
                  "https://gist.github.com/jklolixxs/b53d315cd1cede23af83322c26ce34ec/raw/AmyTelecom.ini"
              },
              {
                label: "Miaona",
                value:
                  "https://gist.github.com/jklolixxs/ff8ddbf2526cafa568d064006a7008e7/raw/Miaona.ini"
              },
              {
                label: "Foo&Friends",
                value:
                  "https://gist.github.com/jklolixxs/df8fda1aa225db44e70c8ac0978a3da4/raw/Foo&Friends.ini"
              },
              {
                label: "ABCloud",
                value:
                  "https://gist.github.com/jklolixxs/b1f91606165b1df82e5481b08fd02e00/raw/ABCloud.ini"
              },
              {
                label: "w8ves",
                value:
                  "https://gist.github.com/jklolixxs/2bdc6a3da8a3ccb896eba568378202a0/raw/w8ves.ini"
              },
              {
                label: "NaNoport",
                value:
                  "https://gist.github.com/jklolixxs/32d4e9a1a5d18a92beccf3be434f7966/raw/NaNoport.ini"
              },
              {
                label: "CordCloud",
                value:
                  "https://gist.github.com/jklolixxs/dfbe0cf71ffc547557395c772836d9a8/raw/CordCloud.ini"
              },
              {
                label: "BigAirport",
                value:
                  "https://gist.github.com/jklolixxs/e2b0105c8be6023f3941816509a4c453/raw/BigAirport.ini"
              },
              {
                label: "N3RO",
                value:
                  "https://gist.github.com/jklolixxs/c54dc6dd3576328b6f393023bcb4752c/raw/N3RO.ini"
              },
              {
                label: "跑路云",
                value:
                  "https://gist.github.com/jklolixxs/9f6989137a2cfcc138c6da4bd4e4cbfc/raw/PaoLuCloud.ini"
              },
              {
                label: "WaveCloud",
                value:
                  "https://gist.github.com/jklolixxs/fccb74b6c0018b3ad7b9ed6d327035b3/raw/WaveCloud.ini"
              },
              {
                label: "几鸡",
                value:
                  "https://gist.github.com/jklolixxs/bfd5061dceeef85e84401482f5c92e42/raw/JiJi.ini"
              },
              {
                label: "四季加速",
                value:
                  "https://gist.github.com/jklolixxs/6ff6e7658033e9b535e24ade072cf374/raw/SJ.ini"
              }
            ]
          },
          {
            label: "自写公开用",
            options: [
              {
                label: "通用·全面·故障转移",
                value:
                  "https://raw.githubusercontent.com/jklolixxs/listes/master/Clash/Config/Full_Fallback.ini"
              },
              {
                label: "通用·全面·故障转移·无去广告",
                value:
                  "https://raw.githubusercontent.com/jklolixxs/listes/master/Clash/Config/Full_Fallback_NoAdB.ini"
              },
              {
                label: "通用·全面·自动选择",
                value:
                  "https://raw.githubusercontent.com/jklolixxs/listes/master/Clash/Config/Full_Urltest.ini"
              },
              {
                label: "通用·全面·自动选择·无去广告",
                value:
                  "https://raw.githubusercontent.com/jklolixxs/listes/master/Clash/Config/Full_Urltest_NoAdB.ini"
              },
              {
                label: "通用·全面·负载均衡",
                value:
                  "https://raw.githubusercontent.com/jklolixxs/listes/master/Clash/Config/Full_Loadbalance.ini"
              },
              {
                label: "通用·全面·负载均衡·无去广告",
                value:
                  "https://raw.githubusercontent.com/jklolixxs/listes/master/Clash/Config/Full_Loadbalance_NoAdB.ini"
              },
              {
                label: "通用·全面",
                value:
                  "https://raw.githubusercontent.com/jklolixxs/listes/master/Clash/Config/Full_Plus.ini"
              },
              {
                label: "通用·全面·无去广告",
                value:
                  "https://raw.githubusercontent.com/jklolixxs/listes/master/Clash/Config/Full_Plus_NoAdB.ini"
              },
              {
                label: "通用·精简",
                value:
                  "https://raw.githubusercontent.com/jklolixxs/listes/master/Clash/Config/Normal_Plus.ini"
              },
              {
                label: "通用·精简·无去广告",
                value:
                  "https://raw.githubusercontent.com/jklolixxs/listes/master/Clash/Config/Normal_Plus_NoAdB.ini"
              },
              {
                label: "通用·流媒体多",
                value:
                  "https://raw.githubusercontent.com/jklolixxs/listes/master/Clash/Config/Video_Plus.ini"
              },
              {
                label: "通用·流媒体多·无去广告",
                value:
                  "https://raw.githubusercontent.com/jklolixxs/listes/master/Clash/Config/Video_Plus_NoAdB.ini"
              }
            ]
          },
          {
            label: "ACL规则",
            options: [
              {
                label: "ACL_默认版",
                value: "https://raw.githubusercontent.com/ACL4SSR/ACL4SSR/master/Clash/config/ACL4SSR.ini"
              },
              {
                label: "ACL_去广告版",
                value: "https://raw.githubusercontent.com/ACL4SSR/ACL4SSR/master/Clash/config/ACL4SSR_AdblockPlus.ini"
              },
              {
                label: "ACL_回国版",
                value: "https://raw.githubusercontent.com/ACL4SSR/ACL4SSR/master/Clash/config/ACL4SSR_BackCN.ini"
              },
              {
                label: "ACL_精简版",
                value: "https://raw.githubusercontent.com/ACL4SSR/ACL4SSR/master/Clash/config/ACL4SSR_Mini.ini"
              },
              {
                label: "ACL_Fallback精简版",
                value: "https://raw.githubusercontent.com/ACL4SSR/ACL4SSR/master/Clash/config/ACL4SSR_Mini_Fallback.ini"
              },
              {
                label: "ACL_多模式精简版",
                value: "https://raw.githubusercontent.com/ACL4SSR/ACL4SSR/master/Clash/config/ACL4SSR_Mini_MultiMode.ini"
              },
              {
                label: "ACL_无测速精简版",
                value: "https://raw.githubusercontent.com/ACL4SSR/ACL4SSR/master/Clash/config/ACL4SSR_Mini_NoAuto.ini"
              },
              {
                label: "ACL_无苹果版",
                value: "https://raw.githubusercontent.com/ACL4SSR/ACL4SSR/master/Clash/config/ACL4SSR_NoApple.ini"
              },
              {
                label: "ACL_无测速版",
                value: "https://raw.githubusercontent.com/ACL4SSR/ACL4SSR/master/Clash/config/ACL4SSR_NoAuto.ini"
              },
              {
                label: "ACL_无测速、苹果版",
                value: "https://raw.githubusercontent.com/ACL4SSR/ACL4SSR/master/Clash/config/ACL4SSR_NoAuto_NoApple.ini"
              },
              {
                label: "ACL_无测速、苹果、微软版",
                value: "https://raw.githubusercontent.com/ACL4SSR/ACL4SSR/master/Clash/config/ACL4SSR_NoAuto_NoApple_NoMicrosoft.ini"
              },
              {
                label: "ACL_无微软版",
                value: "https://raw.githubusercontent.com/ACL4SSR/ACL4SSR/master/Clash/config/ACL4SSR_NoMicrosoft.ini"
              },
              {
                label: "ACL_同步更新版",
                value: "https://raw.githubusercontent.com/ACL4SSR/ACL4SSR/master/Clash/config/ACL4SSR_Online.ini"
              },
              {
                label: "ACL_同步+去广告版",
                value: "https://raw.githubusercontent.com/ACL4SSR/ACL4SSR/master/Clash/config/ACL4SSR_Online_AdblockPlus.ini"
              },
              {
                label: "ACL_同步+全分组版",
                value: "https://raw.githubusercontent.com/ACL4SSR/ACL4SSR/master/Clash/config/ACL4SSR_Online_Full.ini"
              },
              {
                label: "ACL_同步+去广告+全分组版",
                value: "https://raw.githubusercontent.com/ACL4SSR/ACL4SSR/master/Clash/config/ACL4SSR_Online_Full_AdblockPlus.ini"
              },
              {
                label: "ACL_同步+谷歌+全分组版",
                value: "https://raw.githubusercontent.com/ACL4SSR/ACL4SSR/master/Clash/config/ACL4SSR_Online_Full_Google.ini"
              },
              {
                label: "ACL_同步+多模式+全分组版",
                value: "https://raw.githubusercontent.com/ACL4SSR/ACL4SSR/master/Clash/config/ACL4SSR_Online_Full_MultiMode.ini"
              },
              {
                label: "ACL_同步+奈飞+全分组版",
                value: "https://raw.githubusercontent.com/ACL4SSR/ACL4SSR/master/Clash/config/ACL4SSR_Online_Full_Netflix.ini"
              },
              {
                label: "ACL_同步+全分组+无测速版",
                value: "https://raw.githubusercontent.com/ACL4SSR/ACL4SSR/master/Clash/config/ACL4SSR_Online_Full_NoAuto.ini"
              },
              {
                label: "ACL_同步更新精简版",
                value: "https://raw.githubusercontent.com/ACL4SSR/ACL4SSR/master/Clash/config/ACL4SSR_Online_Mini.ini"
              },
              {
                label: "ACL_同步+去广告精简版",
                value: "https://raw.githubusercontent.com/ACL4SSR/ACL4SSR/master/Clash/config/ACL4SSR_Online_Mini_AdblockPlus.ini"
              },
              {
                label: "ACL_同步+Fallback精简版",
                value: "https://raw.githubusercontent.com/ACL4SSR/ACL4SSR/master/Clash/config/ACL4SSR_Online_Mini_Fallback.ini"
              },
              {
                label: "ACL_同步+多国家精简版",
                value: "https://raw.githubusercontent.com/ACL4SSR/ACL4SSR/master/Clash/config/ACL4SSR_Online_Mini_MultiCountry.ini"
              },
              {
                label: "ACL_同步+多模式精简版",
                value: "https://github.com/ACL4SSR/ACL4SSR/blob/master/Clash/config/ACL4SSR_Online_Mini_MultiMode.ini"
              },
              {
                label: "ACL_同步+无测速精简版",
                value: "https://raw.githubusercontent.com/ACL4SSR/ACL4SSR/master/Clash/config/ACL4SSR_Online_Mini_MultiMode.ini"
              },
              {
                label: "ACL_同步+无Reject版",
                value: "https://raw.githubusercontent.com/ACL4SSR/ACL4SSR/master/Clash/config/ACL4SSR_Online_NoReject.ini"
              },
              {
                label: "ACL_白名单版",
                value: "https://raw.githubusercontent.com/ACL4SSR/ACL4SSR/master/Clash/config/ACL4SSR_WithChinaIp.ini"
              },
              {
                label: "ACL_白名单+GFW列表版",
                value: "https://raw.githubusercontent.com/ACL4SSR/ACL4SSR/master/Clash/config/ACL4SSR_WithChinaIp_WithGFW.ini"
              },
              {
                label: "ACL_GFW列表版",
                value: "https://raw.githubusercontent.com/ACL4SSR/ACL4SSR/master/Clash/config/ACL4SSR_WithGFW.ini"
              }
            ]
          },
          {
            label: "全网搜集规则",
            options: [
              {
                label: "常规规则",
                value: "https://raw.githubusercontent.com/flyhigherpi/merlinclash_clash_related/master/Rule_config/ZHANG.ini"
              },
              {
                label: "分区域故障转移",
                value: "https://raw.githubusercontent.com/flyhigherpi/merlinclash_clash_related/master/Rule_config/ZHANG_Area_Fallback.ini"
              },
              {
                label: "分区域自动测速",
                value: "https://raw.githubusercontent.com/flyhigherpi/merlinclash_clash_related/master/Rule_config/ZHANG_Area_Urltest.ini"
              },
              {
                label: "分区域无自动测速",
                value: "https://raw.githubusercontent.com/flyhigherpi/merlinclash_clash_related/master/Rule_config/ZHANG_Area_NoAuto.ini"
              }, 
              {
                label: "OoHHHHHHH",
                value: "https://raw.githubusercontent.com/OoHHHHHHH/ini/master/config.ini"
              },
              {
                label: "CFW-TAP",
                value: "https://raw.githubusercontent.com/OoHHHHHHH/ini/master/cfw-tap.ini"
              },
              {
                label: "lhl77全分组（定期更新）",
                value: "https://raw.githubusercontent.com/lhl77/sub-ini/main/tsutsu-full.ini"
              },
              {
                label: "lhl77简易版（定期更新）",
                value: "https://raw.githubusercontent.com/lhl77/sub-ini/main/tsutsu-mini-gfw.ini"
              },
              {
                label: "ConnersHua 神机规则 Outbound",
                value: "https://gist.githubusercontent.com/tindy2013/1fa08640a9088ac8652dbd40c5d2715b/raw/connershua_new.ini"
              },
              {
                label: "ConnersHua 神机规则 Inbound 回国专用",
                value: "https://gist.githubusercontent.com/tindy2013/1fa08640a9088ac8652dbd40c5d2715b/raw/connershua_backtocn.ini"
              },
              {
                label: "lhie1 洞主规则（使用 Clash 分组规则）",
                value: "https://gist.githubusercontent.com/tindy2013/1fa08640a9088ac8652dbd40c5d2715b/raw/lhie1_clash.ini"
              },
              {
                label: "lhie1 洞主规则完整版",
                value: "https://gist.githubusercontent.com/tindy2013/1fa08640a9088ac8652dbd40c5d2715b/raw/lhie1_dler.ini"
              },
              {
                label: "eHpo1 规则",
                value: "https://gist.githubusercontent.com/tindy2013/1fa08640a9088ac8652dbd40c5d2715b/raw/ehpo1_main.ini"
              },
              {
                label: "多策略组默认白名单模式",
                value: "https://raw.nameless13.com/api/public/dl/ROzQqi2S/white.ini"
              },
              {
                label: "多策略组可以有效减少审计触发",
                value: "https://raw.nameless13.com/api/public/dl/ptLeiO3S/mayinggfw.ini"
              },
              {
                label: "精简策略默认白名单",
                value: "https://raw.nameless13.com/api/public/dl/FWSh3dXz/easy3.ini"
              },
              {
                label: "多策略增加SMTP策略",
                value: "https://raw.nameless13.com/api/public/dl/L_-vxO7I/youtube.ini"
              },
              {
                label: "无策略入门推荐",
                value: "https://raw.nameless13.com/api/public/dl/zKF9vFbb/easy.ini"
              },
              {
                label: "无策略入门推荐国家分组",
                value: "https://raw.nameless13.com/api/public/dl/E69bzCaE/easy2.ini"
              },
              {
                label: "无策略仅IPIP CN + Final",
                value: "https://raw.nameless13.com/api/public/dl/XHr0miMg/ipip.ini"
              },
              {
                label: "无策略魅影vip分组",
                value: "https://raw.nameless13.com/api/public/dl/BBnfb5lD/MAYINGVIP.ini"
              },
              {
                label: "品云专属配置（仅香港区域分组）",
                value: "https://raw.githubusercontent.com/Mazeorz/airports/master/Clash/Examine.ini"
              },
              {
                label: "品云专属配置（全地域分组）",
                value: "https://raw.githubusercontent.com/Mazeorz/airports/master/Clash/Examine_Full.ini"
              },
              {
                label: "nzw9314 规则",
                value: "https://gist.githubusercontent.com/tindy2013/1fa08640a9088ac8652dbd40c5d2715b/raw/nzw9314_custom.ini"
              },
              {
                label: "maicoo-l 规则",
                value: "https://gist.githubusercontent.com/tindy2013/1fa08640a9088ac8652dbd40c5d2715b/raw/maicoo-l_custom.ini"
              },
              {
                label: "DlerCloud Platinum 李哥定制规则",
                value: "https://gist.githubusercontent.com/tindy2013/1fa08640a9088ac8652dbd40c5d2715b/raw/dlercloud_lige_platinum.ini"
              },
              {
                label: "DlerCloud Gold 李哥定制规则",
                value: "https://gist.githubusercontent.com/tindy2013/1fa08640a9088ac8652dbd40c5d2715b/raw/dlercloud_lige_gold.ini"
              },
              {
                label: "DlerCloud Silver 李哥定制规则",
                value: "https://gist.githubusercontent.com/tindy2013/1fa08640a9088ac8652dbd40c5d2715b/raw/dlercloud_lige_silver.ini"
              }
            ]
          },
          {
            label: "各大机场规则",
            options: [
              {
                label: "希腊奶",
                value: "https://raw.githubusercontent.com/MegumiUUU/megumiclash/master/common.ini"
              },    
              {
                label: "路飞船长",
                value: "https://raw.githubusercontent.com/Mazeorz/airports/master/Clash/Luffy_balance.ini"
              },
              {
                label: "CNIX",
                value: "https://raw.githubusercontent.com/Mazeorz/airports/master/Clash/SSRcloud.ini"
              },    
              {
                label: "Nirvana",
                value: "https://raw.githubusercontent.com/Mazetsz/ACL4SSR/master/Clash/config/V2rayPro.ini"
              },
              {
                label: "V2Pro",
                value: "https://raw.githubusercontent.com/Mazeorz/airports/master/Clash/V2Pro.ini"
              },
              {
              label: "史迪仔-自动测速",
              value: "https://raw.githubusercontent.com/Mazeorz/airports/master/Clash/Stitch.ini"
              },
              {
                label: "史迪仔-负载均衡",
                value: "https://raw.githubusercontent.com/Mazeorz/airports/master/Clash/Stitch-Balance.ini"
              },    
              {
                label: "Maying",
                value: "https://subconverter.oss-ap-southeast-1.aliyuncs.com/Rules/RemoteConfig/customized/maying.ini"
              },
              {
                label: "rixCloud",
                value: "https://subconverter.oss-ap-southeast-1.aliyuncs.com/Rules/RemoteConfig/customized/rixcloud.ini"
              },
              {
                label: "YoYu",
                value: "https://subconverter.oss-ap-southeast-1.aliyuncs.com/Rules/RemoteConfig/customized/yoyu.ini"
              },
              {
                label: "Ytoo",
                value: "https://subconverter.oss-ap-southeast-1.aliyuncs.com/Rules/RemoteConfig/customized/ytoo.ini"
              },
              {
                label: "w8ves",
                value: "https://raw.nameless13.com/api/public/dl/M-We_Fn7/w8ves.ini"
              },
              {
                label: "NyanCAT",
                value: "https://subconverter.oss-ap-southeast-1.aliyuncs.com/Rules/RemoteConfig/customized/nyancat.ini"
              },
              {
                label: "Nexitally",
                value: "https://subconverter.oss-ap-southeast-1.aliyuncs.com/Rules/RemoteConfig/customized/nexitally.ini"
              },
              {
                label: "布丁",
                value: "https://subconverter.oss-ap-southeast-1.aliyuncs.com/Rules/RemoteConfig/customized/pud.ini"
              },
              {
                label: "SoCloud",
                value: "https://subconverter.oss-ap-southeast-1.aliyuncs.com/Rules/RemoteConfig/customized/socloud.ini"
              },
              {
                label: "ARK",
                value: "https://subconverter.oss-ap-southeast-1.aliyuncs.com/Rules/RemoteConfig/customized/ark.ini"
              },
              {
                label: "贼船",
                value: "https://subconverter.oss-ap-southeast-1.aliyuncs.com/Rules/RemoteConfig/customized/zeichuan.ini"
              },
              {
                label: "N3RO",
                value: "https://gist.githubusercontent.com/tindy2013/1fa08640a9088ac8652dbd40c5d2715b/raw/n3ro_optimized.ini"
              },
              {
                label: "Scholar",
                value: "https://gist.githubusercontent.com/tindy2013/1fa08640a9088ac8652dbd40c5d2715b/raw/scholar_optimized.ini"
              },
              {
                label: "便利店",
                value: "https://subweb.oss-cn-hongkong.aliyuncs.com/RemoteConfig/customized/convenience.ini"
              },
              {
                label: "ssrCloud",
                value: "https://subconverter.oss-ap-southeast-1.aliyuncs.com/Rules/RemoteConfig/customized/ssrcloud.ini"
              }
            ]
          },
          {
            label: "特殊",
            options: [
              {
                label: "NeteaseUnblock",
                value: "https://subconverter.oss-ap-southeast-1.aliyuncs.com/Rules/RemoteConfig/special/netease.ini"
              },
              {
                label: "Basic",
                value: "https://subconverter.oss-ap-southeast-1.aliyuncs.com/Rules/RemoteConfig/special/basic.ini"
              }
            ]
          }
        ]
      },
      form: {
        sourceSubUrl: "",
        clientType: "",
        customBackend: "https://www.chien.asia/sub?",
        shortType: "https://suo.yt/short",
        remoteConfig: "https://raw.githubusercontent.com/ACL4SSR/ACL4SSR/master/Clash/config/ACL4SSR.ini",
        excludeRemarks: "",
        includeRemarks: "",
        filename: "",
        rename: "",
        emoji: true,
        nodeList: false,
        extraset: false,
        sort: false,
        udp: false,
        tfo: false,
        expand: true,
        scv: false,
        fdn: false,
        appendType: false,
        insert: false, // 是否插入默认订阅的节点，对应配置项 insert_url
        new_name: true, // 是否使用 Clash 新字段

        // tpl 定制功能
        tpl: {
          surge: {
            doh: false // dns 查询是否使用 DoH
          },
          clash: {
            doh: false
          }
        }
      },

      loading: false,
      customSubUrl: "",
      curtomShortSubUrl: "",

      dialogUploadConfigVisible: false,
      uploadConfig: "",
      uploadPassword: "",
      myBot: tgBotLink,
      sampleConfig: remoteConfigSample
    };
  },
  created() {
    document.title = "HsiChien's订阅转换";
    this.isPC = this.$getOS().isPc;
  },
  mounted() {
    this.form.clientType = "clash";
    this.getBackendVersion();
  },
  methods: {
    onCopy() {
      this.$message.success("Copied!");
    },
    goToProject() {
      window.open(project);
    },
    gotoTgChannel() {
      window.open(tgBotLink);
    },
    gotoGayhub() {
      window.open(gayhubRelease);
    },
    gotoRemoteConfig() {
      window.open(remoteConfigSample);
    },
    clashInstall() {
      if (this.customSubUrl === "") {
        this.$message.error("请先填写必填项，生成订阅链接");
        return false;
      }

      const url = "clash://install-config?url=";
      window.open(
        url +
          encodeURIComponent(
            this.curtomShortSubUrl !== ""
              ? this.curtomShortSubUrl
              : this.customSubUrl
          )
      );
    },
    surgeInstall() {
      if (this.customSubUrl === "") {
        this.$message.error("请先填写必填项，生成订阅链接");
        return false;
      }


      const url = "surge://install-config?url=";
      window.open(url + this.customSubUrl);
    },
    gotovideo() {
    this.$alert("YouTube测速视频冲冲冲！",{
	type: "warning",
	confirmButtonText: '确定',
	customClass: 'msgbox',
	showClose: false ,
	})
    .then(() => {
        const youtube = 'https://www.youtube.com/watch?v=egSvdEJZRBk'
        window.open(youtube);
        });
    },
    makeUrl() {
      if (this.form.sourceSubUrl === "" || this.form.clientType === "") {
        this.$message.error("订阅链接与客户端为必填项");
        return false;
      }
      if (this.form.sourceSubUrl.indexOf("losadhwse") !== -1 && (this.form.customBackend.indexOf("azure.jkloli.top") !== -1)) {
        this.$alert("薯条已将该后端屏蔽，请更换其他后端进行转换！",{
        type: "warning",
        confirmButtonText: '确定',
        customClass: 'msgbox',
        showClose: false ,
        });
        return false;
      }

      let backend =
        this.form.customBackend === ""
          ? defaultBackend
          : this.form.customBackend;

      let sourceSub = this.form.sourceSubUrl;
      sourceSub = sourceSub.replace(/(\n|\r|\n\r)/g, "|");

      this.customSubUrl =
        backend +
        "target=" +
        this.form.clientType +
        "&url=" +
        encodeURIComponent(sourceSub) +
        "&insert=" +
        this.form.insert;

      if (this.advanced === "2") {
        if (this.form.remoteConfig !== "") {
          this.customSubUrl +=
            "&config=" + encodeURIComponent(this.form.remoteConfig);
        }
        if (this.form.excludeRemarks !== "") {
          this.customSubUrl +=
            "&exclude=" + encodeURIComponent(this.form.excludeRemarks);
        }
        if (this.form.includeRemarks !== "") {
          this.customSubUrl +=
            "&include=" + encodeURIComponent(this.form.includeRemarks);
        }
        if (this.form.filename !== "") {
          this.customSubUrl +=
            "&filename=" + encodeURIComponent(this.form.filename);
        }
        if (this.form.rename !== "") {
          this.customSubUrl +=
            "&rename=" + encodeURIComponent(this.form.rename);
        }
        if (this.form.appendType) {
          this.customSubUrl +=
            "&append_type=" + this.form.appendType.toString();
        }

        this.customSubUrl +=
          "&emoji=" +
          this.form.emoji.toString() +
          "&list=" +
          this.form.nodeList.toString() +
          "&udp=" +
          this.form.udp.toString() +
          "&tfo=" +
          this.form.tfo.toString() +
          "&expand=" +
          this.form.expand.toString() +
          "&scv=" +
          this.form.scv.toString() +
          "&fdn=" +
          this.form.fdn.toString() +
          "&sort=" +
          this.form.sort.toString();

        if (this.form.tpl.surge.doh === true) {
          this.customSubUrl += "&surge.doh=true";
        }

        if (this.form.clientType === "clash") {
          if (this.form.tpl.clash.doh === true) {
            this.customSubUrl += "&clash.doh=true";
          }

          this.customSubUrl += "&new_name=" + this.form.new_name.toString();
        }
      }

      this.$copyText(this.customSubUrl);
      this.$message.success("定制订阅已复制到剪贴板");
    },
    makeShortUrl() {
      if (this.customSubUrl === "") {
        this.$message.warning("请先生成订阅链接，再获取对应短链接");
        return false;
      }
      
      let duan =
        this.form.shortType === ""
          ? shortUrlBackend
          : this.form.shortType;
      
      this.loading = true;

      let data = new FormData();
      data.append("longUrl", btoa(this.customSubUrl));

      this.$axios
        .post(duan, data, {
          header: {
            "Content-Type": "application/form-data; charset=utf-8"
          }
        })
        .then(res => {
          if (res.data.Code === 1 && res.data.ShortUrl !== "") {
            this.curtomShortSubUrl = res.data.ShortUrl;
            this.$copyText(res.data.ShortUrl);
            this.$message.success("短链接已复制到剪贴板");
          } else {
            this.$message.error("短链接获取失败：" + res.data.Message);
          }
        })
        .catch(() => {
          this.$message.error("短链接获取失败");
        })
        .finally(() => {
          this.loading = false;
        });
    },
    confirmUploadConfig() {
      if (this.uploadConfig === "") {
        this.$message.warning("远程配置不能为空");
        return false;
      }

      this.loading = true;

      let data = new FormData();
      data.append("password", this.uploadPassword);
      data.append("config", this.uploadConfig);

      this.$axios
        .post(configUploadBackend, data, {
          header: {
            "Content-Type": "application/form-data; charset=utf-8"
          }
        })
        .then(res => {
          if (res.data.Code === 1 && res.data.url !== "") {
            this.$message.success(
              "远程配置上传成功，配置链接已复制到剪贴板，有效期三个月望知悉"
            );

            // 自动填充至『表单-远程配置』
            this.form.remoteConfig = res.data.Url;
            this.$copyText(this.form.remoteConfig);

            this.dialogUploadConfigVisible = false;
          } else {
            this.$message.error("远程配置上传失败：" + res.data.Message);
          }
        })
        .catch(() => {
          this.$message.error("远程配置上传失败");
        })
        .finally(() => {
          this.loading = false;
        });
    },
    backendSearch(queryString, cb) {
      let backends = this.options.backendOptions;

      let results = queryString
        ? backends.filter(this.createFilter(queryString))
        : backends;

      // 调用 callback 返回建议列表的数据
      cb(results);
    },
    createFilter(queryString) {
      return candidate => {
        return (
          candidate.value.toLowerCase().indexOf(queryString.toLowerCase()) === 0
        );
      };
    },
    getBackendVersion() {
      this.$axios
        .get(
          defaultBackend.substring(0, defaultBackend.length - 5) + "/version"
        )
        .then(res => {
          this.backendVersion = res.data.replace(/backend\n$/gm, "");
          this.backendVersion = this.backendVersion.replace("subconverter", "");
        });
    }
  }
};
</script>
