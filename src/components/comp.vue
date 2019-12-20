<template>
  <el-row type="flex" justify="center">
    <el-col :span="12" style="margin-top: 35px">
      <h2>
        <i class="el-icon-upload2" style="margin-right: 10px"></i>上传文件
      </h2>
      <input type="file" ref="myFile" @change="selectedFile" />

      <div style="margin-top: 70px" v-if="this.ft == 'text'">
        <h2>
          <i class="el-icon-document" style="margin-right: 10px"></i>文件信息
        </h2>
        <el-card shadow="hover">
          <p style="font-size: 8px">文件名称 {{ filename }}</p>
          <p style="font-size: 8px">文件类型 {{ filetype }}</p>
          <p style="font-size: 8px">文件尺寸 {{ filesize }} KB</p>
          <el-card shadow="never">{{ text }}</el-card>
        </el-card>
      </div>

      <div style="margin-top: 70px" v-if="this.ft == 'img'">
        <h2>
          <i class="el-icon-document" style="margin-right: 10px"></i>文件信息
        </h2>
        <el-card shadow="hover">
          <p style="font-size: 8px">文件名称 {{ filename }}</p>
          <p style="font-size: 8px">文件类型 {{ filetype }}</p>
          <p style="font-size: 8px">文件尺寸 {{ filesize }} KB</p>
          <el-image :src="imgsrc"></el-image>
        </el-card>
      </div>

      <div v-if="this.ft !==''" style="margin-top: 70px">
        <h2>
          <i class="el-icon-cpu" style="margin-right: 10px"></i>算法选项
        </h2>
        <el-select v-model="algorithm" placeholder="请选择">
          <el-option
            v-for="item in algorithms"
            :key="item.value"
            :label="item.lable"
            :value="item.value"
          ></el-option>
        </el-select>
      </div>

      <div v-if="this.ft !==''" style="margin-top: 70px">
        <h2>
          <i class="el-icon-check" style="margin-right: 10px"></i>送出至服务器
        </h2>
        <el-button type="primary" @click="submitfile">送出至服务器</el-button>
      </div>

      <div style="margin-top: 70px" v-if="this.ft == 'text'">
        <h2>
          <i class="el-icon-document" style="margin-right: 10px"></i>计算结果
        </h2>
        <el-card shadow="hover">
          <el-card shadow="never">{{ newtext }}</el-card>
        </el-card>
      </div>

      <div style="margin-top: 70px" v-if="this.ft == 'img'">
        <h2>
          <i class="el-icon-document" style="margin-right: 10px"></i>计算结果
        </h2>
        <el-card shadow="hover">
          <el-image :src="newimgsrc"></el-image>
        </el-card>
      </div>
    </el-col>
  </el-row>
</template>

<script>

const backendURL = "http://localhost:3000";

export default {
  name: "comp",
  data() {
    return {
      text: "",
      newtext: "",
      filetype: "",
      filesize: "",
      imgsrc: "",
      newimgsrc: "",
      ft: "",
      algorithm: "",
      algorithms: [
        { value: 1, lable: "算法 #1" },
        { value: 2, lable: "算法 #2" },
        { value: 3, lable: "算法 #3" },
        { value: 4, lable: "算法 #4" },
        { value: 5, lable: "算法 #5" },
        { value: 6, lable: "算法 #6" },
        { value: 7, lable: "算法 #7" },
        { value: 8, lable: "算法 #8" },
        { value: 9, lable: "算法 #9" },
        { value: 10, lable: "算法 #10" }
      ]
    };
  },
  methods: {
    selectedFile() {
      let file = this.$refs.myFile.files[0];

      if (!file) return;

      this.filesize = file.size;
      this.filename = file.name;

      if (file.type == "text/plain") {
        this.ft = "text";
        this.filetype = "文本格式 " + file.type.split("/")[1];
        let reader = new FileReader();
        reader.readAsText(file, "UTF-8");
        reader.onload = evt => {
          this.text = evt.target.result;
        };
        reader.onerror = evt => {
          alert(evt);
        };
      } else if (file.type.split("/")[0] === "image") {
        this.ft = "img";
        this.filetype = "图像格式 " + file.type.split("/")[1];
        this.imgsrc = URL.createObjectURL(file);
      }
    },
    submitfile() {
      var self = this;
      let formData = new FormData();
      formData.append("file", this.$refs.myFile.files[0]);
      formData.append("algorithm", this.algorithm);
      this.$axios
        .post(backendURL + "/upload", formData, {
          headers: {
            "Content-Type": "multipart/form-data"
          }
        })
        .then(function(response) {
          if (self.ft == "text") {
            self.newtext = response.data.text;
          } else if (self.ft == "img") {
            self.newimgsrc = backendURL + "/" + response.data.filename;
          }
        })
        .catch(function(error) {
          alert(error);
        });
    }
  }
};
</script>