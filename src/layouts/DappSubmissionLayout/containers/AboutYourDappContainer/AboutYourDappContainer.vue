<template>
  <div class="about-your-dapp w-50 mb-5 mt-5">
    <b-form onsubmit="return false;">
      <b-form-group>
        <label class="dapp-label">Dapp name</label>
        <div class="dapp-input">
          <b-form-input
            v-validate="'required'"
            id="dappName"
            v-model="form.dappName"
            name="name"
            type="text"
            @update="updateName"
          >
          </b-form-input>
          <span>*</span>
        </div>
        <p v-if="errors.has('name')" class="error">
          {{ errors.first('name') }}
        </p>
      </b-form-group>
      <b-form-group>
        <label class="dapp-label">Category</label>
        <div class="dapp-input">
          <label class="dapp-select-label">
            <b-form-select
              v-validate="'required'"
              id="dappCategory"
              :options="dappCategories"
              v-model="form.category"
              name="category"
              @change="updateCategory"
            ></b-form-select>
          </label>
          <span>*</span>
        </div>
        <p v-if="errors.has('category')" class="error">
          {{ errors.first('category') }}
        </p>
      </b-form-group>
      <b-form-group class="input-tags-group tags-form-group">
        <label class="dapp-label"
          >Tags
          <popover
            :popcontent="$t('dappsSubmission.tags')"
            class="dapp-popover"
          ></popover>
        </label>
        <div class="input-tags-container">
          <div class="tag-wrapper">
            <div ref="tagHolder" class="tag-holder">
              <tag-component
                v-for="(tag, idx) in displayTags"
                ref="tagContainer"
                :name="tag"
                :delete-func="deleteTag"
                :idx="idx"
                :key="tag + idx"
              />
            </div>
            <div class="dapp-input">
              <input
                id="dappTags"
                ref="dappTagsInput"
                v-model="tagInput"
                class="dapp-tags-input"
                type="text"
                @keyup="onKeyDown"
              />
            </div>
          </div>
          <span>*</span>
        </div>
        <p v-if="dappTagsError" class="error">
          The tags field is required
        </p>
      </b-form-group>
      <div class="suggested-tags-container mb-3">
        <p class="title">Suggested Tags</p>
        <p class="tags">
          <span>Games</span>
          <span>Defi Lending</span>
          <span>Social</span>
          <span>Finance</span>
          <span>Wallet</span>
        </p>
      </div>
      <b-form-group id="form-group-description">
        <label class="dapp-label">Description</label>
        <b-form-invalid-feedback id="form-group-description">
          This is a required field.
        </b-form-invalid-feedback>
        <div class="dapp-input">
          <b-form-textarea
            v-validate="'max:800|required'"
            v-model="form.description"
            name="description"
            placeholder="800 characters"
            rows="7"
            size="lg"
            @update="updateDescription"
            @change="updateDisableBtn(errors)"
          ></b-form-textarea>
          <span>*</span>
        </div>
        <p v-if="errors.has('description')" class="error">
          {{ errors.first('description') }}
        </p>
      </b-form-group>
      <b-form-group>
        <label class="dapp-label"
          >Is your Dapp available for use in the United States?</label
        >
        <div class="dapp-input">
          <label class="dapp-select-label">
            <b-form-select
              v-validate="'required'"
              id="dappUsMarket"
              :options="dappUsMarketOptions"
              v-model="form.usMarket"
              name="field"
              @change="updateUsMarket"
            ></b-form-select>
          </label>
          <span>*</span>
        </div>
        <p v-if="errors.has('field')" class="error">
          {{ errors.first('field') }}
        </p>
      </b-form-group>
      <b-form-group>
        <label class="dapp-label">Dapp status</label>
        <div class="dapp-input">
          <label class="dapp-select-label">
            <b-form-select
              v-validate="'required'"
              id="dappStatus"
              :options="dappStatusOptions"
              v-model="form.dappStatus"
              name="status"
              @change="updateDappStatus"
            ></b-form-select>
          </label>
          <span>*</span>
        </div>
        <p v-if="errors.has('status')" class="error">
          {{ errors.first('status') }}
        </p>
      </b-form-group>
      <b-form-group>
        <label class="dapp-label"
          >Mock user flow
          <popover
            :popcontent="$t('dappsSubmission.mockUserFlow')"
            class="dapp-popover"
          ></popover>
        </label>
        <p class="dapp-text-info mb-3">
          Dimensions must be 1200px width by 630px height; JPEG or PDF file
          only; Image size no large than 5MB
        </p>
        <div class="dapp-input">
          <label for="customUpload" class="upload-btn-wrapper">
            <div class="fake-input">
              <p class="file-name">{{ form.mockFlowFile.name }}</p>
            </div>
            <button class="upload-btn">Upload</button>
          </label>
          <input
            id="customUpload"
            ref="mockUserFlow"
            type="file"
            @change="onMockFileChange"
          />
          <span>*</span>
        </div>
        <p v-show="mockFileError" class="error">
          {{ mockFileError }}
        </p>
      </b-form-group>
      <b-form-group>
        <label class="dapp-label"
          >Contract address
          <popover
            :popcontent="$t('dappsSubmission.contractAddress')"
            class="dapp-popover"
          ></popover>
        </label>
        <div class="dapp-input">
          <b-form-input
            v-validate="'required'"
            id="contractAddress"
            v-model="form.contractAddress"
            name="address"
            type="text"
            @update="updateContractAddress"
          >
          </b-form-input>
          <span>*</span>
        </div>
        <p v-if="errors.has('address')" class="error">
          {{ errors.first('address') }}
        </p>
      </b-form-group>
      <b-form-group>
        <label class="dapp-label">
          <span class="dapp-icon-label"> Dapp icon </span>
          <popover
            :popcontent="$t('dappsSubmission.dappIcon')"
            class="dapp-popover"
          ></popover>
          <label v-if="form.dappIconUrl" for="dappIcon" class="replace-label"
            >Replace</label
          >
        </label>
        <div class="image-wrapper">
          <div
            :class="form.dappIconUrl ? 'dapp-icon-uploaded ' : ''"
            class="image-container"
          >
            <label class="image-label" for="dappIcon">
              <div class="image-placeholder">
                <i v-if="!form.dappIconUrl" class="fa fa-cloud-upload"></i>
                <h4 class="image-text">
                  Drop your icon here, or select a file from your computer.
                </h4>
                <p class="image-requirements">
                  JPEG or PNG, at least 192px * 192px
                </p>
              </div>
              <img
                v-if="form.dappIconUrl"
                :src="form.dappIconUrl"
                class="dapp-icon-img"
              />
              <b-form-file
                id="dappIcon"
                drop-placeholder="Drop your icon here"
                type="file"
                @change="onDappIconChange"
              ></b-form-file>
            </label>
          </div>
          <span
            :class="form.dappIconUrl ? 'uploaded-required-icon' : ''"
            class="requiredIcon"
            >*</span
          >
        </div>
        <p v-show="dappIconError" class="error">
          {{ dappIconError }}
        </p>
      </b-form-group>
      <b-form-group>
        <label class="dapp-label"
          >Banner
          <popover
            :popcontent="$t('dappsSubmission.banner')"
            class="dapp-popover"
          ></popover>
          <label v-if="form.bannerUrl" for="bannerImage" class="replace-label"
            >Replace</label
          >
        </label>
        <div class="image-wrapper">
          <div
            :class="form.bannerUrl ? 'banner-uploaded ' : ''"
            class="image-container"
          >
            <label class="image-label" for="bannerImage">
              <div class="image-placeholder">
                <i v-if="!form.bannerUrl" class="fa fa-cloud-upload"></i>
                <h4 class="image-text">
                  Drop your image here, or select a file from your computer.
                </h4>
                <p class="image-requirements">
                  JPEG or PNG, at least 1200px * 206px
                </p>
              </div>
              <img
                v-if="form.bannerUrl"
                :src="form.bannerUrl"
                class="banner-img"
              />
              <b-form-file
                id="bannerImage"
                accept="image/*"
                drop-placeholder="Drop your banner here"
                type="file"
                @change="onBannerChange"
              >
              </b-form-file>
            </label>
          </div>
          <span
            :class="form.bannerUrl ? 'uploaded-required-icon' : ''"
            class="requiredIcon"
            >*</span
          >
        </div>
        <p v-show="bannerError" class="error">
          {{ bannerError }}
        </p>
      </b-form-group>
      <b-form-group>
        <label class="dapp-label">Dapp website</label>
        <b-form-input
          v-validate="'url:require_protocol'"
          id="dappWebsite"
          v-model="form.dappWebsite"
          name="website"
          placeholder="URL link"
          type="text"
          @change="updateDisableBtn(errors)"
        ></b-form-input>
        <p v-if="errors.has('website')" class="error">
          {{ errors.first('website') }}
        </p>
      </b-form-group>
      <b-form-group>
        <label class="dapp-label"
          >Dapp contract audit
          <popover
            :popcontent="$t('dappsSubmission.contractAudit')"
            class="dapp-popover"
          ></popover>
        </label>
        <label class="dapp-select-label">
          <b-form-select
            id="dappContract"
            v-model="form.contractAudit"
            :options="dappContractOptions"
          ></b-form-select>
        </label>
      </b-form-group>
    </b-form>
  </div>
</template>

<script>
import TagComponentVue from '../../components/TagComponent/TagComponent.vue';
import PopOver from '@/components/PopOver';

export default {
  components: {
    'tag-component': TagComponentVue,
    popover: PopOver
  },
  props: {
    updateName: {
      type: Function,
      default: () => {}
    },
    updateCategory: {
      type: Function,
      default: () => {}
    },
    updateTags: {
      type: Function,
      default: () => {}
    },
    updateDescription: {
      type: Function,
      default: () => {}
    },
    updateDisableBtn: {
      type: Function,
      default: () => {}
    },
    updateUsMarket: {
      type: Function,
      default: () => {}
    },
    updateDappStatus: {
      type: Function,
      default: () => {}
    },
    updateMockFlow: {
      type: Function,
      default: () => {}
    },
    updateContractAddress: {
      type: Function,
      default: () => {}
    },
    updateDappIcon: {
      type: Function,
      default: () => {}
    },
    updateBanner: {
      type: Function,
      default: () => {}
    },
    form: {
      type: Object,
      default: function() {
        return {};
      }
    }
  },
  data() {
    return {
      tag: '',
      tags: [],
      dappCategories: [
        { value: null, text: 'Please select' },
        { value: 'Games', text: 'Games' },
        { value: 'Social', text: 'Social' },
        { value: 'Finance', text: 'Finance' },
        { value: 'Infrastructure', text: 'Infrastructure' },
        { value: 'Exchanges', text: 'Exchanges' },
        { value: 'Development', text: 'Development' },
        { value: 'Media', text: 'Media' },
        { value: 'Wallet', text: 'Wallet' },
        { value: 'Security', text: 'Security' },
        { value: 'Property', text: 'Property' },
        { value: 'Marketplaces', text: 'Marketplaces' },
        { value: 'Governance', text: 'Governance' },
        { value: 'Storage', text: 'Storage' },
        { value: 'Identity', text: 'Identity' },
        { value: 'Energy', text: 'Energy' },
        { value: 'Insurance', text: 'Insurance' },
        { value: 'Health', text: 'Health' },
        { value: 'Other', text: 'Other' }
      ],
      dappUsMarketOptions: [
        { value: null, text: 'Please select' },
        { value: 'Yes', text: 'Yes' },
        { value: 'No', text: 'No' }
      ],
      dappStatusOptions: [
        { value: null, text: 'Please select' },
        { value: 'Live', text: 'Live' },
        { value: 'Beta', text: 'Beta' },
        { value: 'Prototype', text: 'Prototype' },
        { value: 'Work in progress', text: 'Work in progress' },
        { value: 'Concept', text: 'Concept' },
        { value: 'Stealth', text: 'Stealth' }
      ],
      dappContractOptions: [
        { value: null, text: 'Please select' },
        { value: 'None in Process', text: 'None in Process' },
        { value: 'In Process', text: 'In Process' },
        { value: 'Completed', text: 'Completed' }
      ],
      tagInput: '',
      displayTags: this.form.tags,
      mockFileError: null,
      dappIconError: null,
      bannerError: null,
      mockFlowImgName: '',
      dappTagsError: false
    };
  },
  methods: {
    onKeyDown(e) {
      if (
        e.keyCode === 13 &&
        this.$refs.tagHolder.offsetWidth <=
          this.$refs.dappTagsInput.offsetWidth - 10
      ) {
        if (this.tagInput.length > 0) {
          this.displayTags.push(this.tagInput);
          this.tagInput = '';
          setTimeout(() => this.updateWidth());
        }
      } else if (e.keyCode === 8) {
        if (this.tagInput.length <= 0) {
          const lastTag = this.displayTags.splice(
            this.displayTags.length - 1,
            1
          );
          setTimeout(() => {
            this.updateWidth();
            this.tagInput = lastTag.toString();
          });
        }
      }
      this.dappTagsError = this.displayTags.length === 0;
      this.updateTags(this.displayTags);
    },
    deleteTag(idx) {
      this.displayTags.splice(idx, 1);
      this.updateTags(this.displayTags);
      this.dappTagsError = this.displayTags.length === 0;
      setTimeout(() => this.updateWidth());
    },
    updateWidth() {
      this.$refs.dappTagsInput.style.paddingLeft =
        this.$refs.tagHolder.offsetWidth > 0
          ? `${this.$refs.tagHolder.offsetWidth + 8}px`
          : '10.5px';
    },
    onMockFileChange(e) {
      const file = e.target.files[0],
        img = new Image(),
        url = URL.createObjectURL(file),
        vm = this;

      this.form.mockFlowFile = '';
      this.form.mockFlowUrl = '';

      if (!url) {
        this.onMockFileChangeError('uploadError');
      }

      if (file.type === 'application/pdf') {
        file.size > 5000000
          ? this.onMockFileChangeError('exceededSize')
          : this.onMockFileChangeSuccess(file, url);
      } else {
        img.src = url;

        img.onload = function() {
          img.width > 1200 || img.height > 630
            ? vm.onMockFileChangeError('exceededSize')
            : vm.onMockFileChangeSuccess(file, url);
        };

        img.onerror = function() {
          vm.onMockFileChangeError('uploadError');
        };
      }
    },
    onMockFileChangeSuccess(file, url) {
      this.mockFileError = null;
      this.form.mockFlowFile = file;
      this.form.mockFlowUrl = url;
      this.updateMockFlow(false);
    },
    onMockFileChangeError(type) {
      this.mockFileError =
        type === 'exceededSize'
          ? 'The image dimensions are too big. Dimensions must be 1200px width by 630px height and less than 5MB.'
          : 'Upload error. Please try a different file.';
      this.updateMockFlow(true);
    },
    onDappIconChange(e) {
      const file =
          e.type === 'drop' ? e.dataTransfer.files[0] : e.target.files[0],
        img = new Image(),
        url = URL.createObjectURL(file),
        vm = this;

      img.src = url;
      vm.form.dappIconFile = '';
      vm.form.dappIconUrl = '';

      img.onload = function() {
        if (img.height > 192 || img.width > 192) {
          vm.dappIconError =
            'The image dimensions are too big. Dimensions must be 192px by 192px.';
          vm.updateDappIcon(true);
        } else {
          vm.dappIconError = null;
          vm.form.dappIconFile = file;
          vm.form.dappIconUrl = url;
          vm.updateDappIcon(false);
        }
      };

      img.onerror = function() {
        vm.dappIconError = 'Upload error. Please try a different file.';
        vm.updateDappIcon(true);
      };
    },
    onBannerChange(e) {
      const file =
          e.type === 'drop' ? e.dataTransfer.files[0] : e.target.files[0],
        img = new Image(),
        url = URL.createObjectURL(file),
        vm = this;

      vm.form.bannerFile = '';
      vm.form.bannerUrl = '';
      img.src = url;

      img.onload = function() {
        if (img.width < 1200 || img.height < 206) {
          vm.bannerError =
            'The image dimensions are too small. Dimensions must be at least 1200px* 206px.';
          vm.updateBanner(true);
        } else {
          vm.bannerError = null;
          vm.form.bannerFile = file;
          vm.form.bannerUrl = url;
          vm.updateBanner(false);
        }
      };

      img.onerror = function() {
        vm.bannerError = 'Upload error. Please try a different file.';
        vm.updateBanner(true);
      };
    }
  }
};
</script>

<style lang="scss" scoped>
@import 'AboutYourDappContainer.scss';
@import '../AboutYourDappTeamContainer.scss';
</style>

<style lang="scss">
.dapp-label {
  align-items: center;
  display: flex;
  font-size: 16px;
  margin-bottom: 10px;
  width: 90%;
}
.custom-file-input {
  height: 100%;
}
.custom-file-label {
  height: 100%;
  opacity: 0;
  display: flex;
  align-items: center;
  justify-content: center;

  &.dragging {
    opacity: 1;
    border-color: #05c0a5;
  }
}

.custom-file-label::after {
  opacity: 0;
}
</style>
