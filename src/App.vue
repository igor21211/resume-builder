<template>
  <main class="container">
    <SideBar>
      <EditToggle @edit-mode-toggled="toggleEditMode" label="Edit Mode" />
      <div class="sidebar-section" v-if="!editMode">
        <SelectInput
          label="Resume format"
          :options="[
            { name: 'A4', value: 'a4' },
            { name: 'Letter', value: 'letter' }
          ]"
          :default-value="resumeFormat"
          @upgrade-selection="resumeFormat = $event"
        />
        <ExportPdf v-if="!editMode" :resume-format="resumeFormat" />
      </div>
      <div class="sidebar-section" v-if="editMode">
        <div class="sidebar-title">Left column</div>
        <ColorInput
          label="Highline color"
          @color-changed="colors.left.highlight = $event"
          :default-color="colors.left.highlight"
        />
        <ColorInput
          label="Text color"
          @color-changed="colors.left.text = $event"
          :default-color="colors.left.text"
        />
        <ColorInput
          label="Background color"
          @color-changed="colors.left.background = $event"
          :default-color="colors.left.background"
        />
      </div>
      <div class="sidebar-section" v-if="editMode">
        <div class="sidebar-title">Right column</div>
        <ColorInput
          label="Highline color"
          @color-changed="colors.right.highlight = $event"
          :default-color="colors.right.highlight"
        />
        <ColorInput
          label="Text color"
          @color-changed="colors.right.text = $event"
          :default-color="colors.right.text"
        />
        <ColorInput
          label="Background color"
          @color-changed="colors.right.background = $event"
          :default-color="colors.right.background"
        />
      </div>
      <div class="sidebar-section" v-if="editMode">
        <PercentInput
          label="Left column width"
          @percent-changed="widthLeft = $event"
          :current-value="widthLeft"
          :min="20"
          :max="80"
        />
        <SelectInput
          label="Headline weight"
          :options="[
            { name: 'Thin', value: '300' },
            { name: 'Medium', value: '400' },
            { name: 'Thick', value: '600' }
          ]"
          :default-value="headlineWeight"
          @upgrade-selection="headlineWeight = $event"
        />
      </div>
      <div class="sidebar-section" v-if="editMode">
        <EditToggle label="Show photo" @edit-mode-toggled="toggleImage" />
        <SelectInput
          v-if="showImage"
          label="Image shape"
          :options="[
            { name: 'Square', value: 'square' },
            { name: 'Round', value: 'round' }
          ]"
          :default-value="imageShape"
          @upgrade-selection="imageShape = $event"
        />

        <ImageUpload v-if="showImage" label="Upload photo" @image-changed="imgSrc = $event" />
      </div>
    </SideBar>
    <div class="resume-wrapper">
      <CustomButtons btn-type="primary-right" @click="saveConfig"
        >Save configuration in browser</CustomButtons
      >
      <div
        id="resume"
        class="d-flex"
        :class="{ 'edit-off': !editMode, 'letter-format': resumeFormat == 'letter' }"
        :style="cssVariables"
      >
        <div class="left-col" :style="{ width: percentageWidthLeft }">
          <ResumeSection>
            <img
              v-if="showImage"
              :src="imgSrc"
              alt="photo"
              class="profile-pic"
              :class="{ circle: imageShape == 'round' }"
            />
            <SectionHeadline
              :headline="headlines[0]"
              @headline-edited="updateHeadline($event, 0)"
              :editMode="editMode"
            />
            <div :contenteditable="editMode" @input="updateProperty($event, 'introText')">
              {{ introText }}
            </div>
          </ResumeSection>

          <ResumeSection>
            <SectionHeadline
              :headline="headlines[1]"
              @headline-edited="updateHeadline($event, 1)"
              :editMode="editMode"
            />
            <contact-component
              :contact="contact"
              @contact-edited="updateNestedProperty"
              :editMode="editMode"
            />
          </ResumeSection>

          <ResumeSection>
            <SectionHeadline
              :headline="headlines[2]"
              @headline-edited="updateHeadline($event, 2)"
              :editMode="editMode"
            />
            <ul>
              <li
                v-for="(skill, index) in skills"
                :key="index"
                :contenteditable="editMode"
                @input="updateNestedProperty($event, 'skills', index)"
              >
                {{ skill }}
              </li>
            </ul>
            <EditButtons
              @add-click="addClick('skills')"
              @remove-click="removeClick(this.skills.length - 1, 'skills')"
              :show-remove-btn="this.skills.length > 0"
            />
          </ResumeSection>

          <ResumeSection>
            <SectionHeadline
              :headline="headlines[3]"
              @headline-edited="updateHeadline($event, 3)"
              :editMode="editMode"
            />
            <ul>
              <li
                v-for="(certification, index) in certifications"
                :key="index"
                :contenteditable="editMode"
                @input="updateNestedProperty($event, 'certifications', index)"
              >
                {{ certification }}
              </li>
            </ul>
            <EditButtons
              @add-click="addClick('certifications')"
              @remove-click="removeClick(this.certifications.length - 1, 'certifications')"
              :show-remove-btn="this.certifications.length > 0"
            />
          </ResumeSection>
        </div>

        <div class="right-col">
          <div
            class="personal-name"
            :contenteditable="editMode"
            @input="updateProperty($event, 'name')"
          >
            {{ name }}
          </div>

          <div
            class="personal-title"
            :contenteditable="editMode"
            @input="updateProperty($event, 'title')"
          >
            {{ title }}
          </div>

          <ResumeSection>
            <div class="d-flex">
              <SectionHeadline
                :headline="headlines[4]"
                @headline-edited="updateHeadline($event, 4)"
                :editMode="editMode"
              />
              <EditButtons
                @add-click="addExperience"
                :show-remove-btn="false"
                text-add="Add Experience"
              />
            </div>

            <div v-for="(expire, index) in experience" :key="index" class="inner-section">
              <div class="d-flex justify-content-between">
                <div :contenteditable="editMode" @input="updateExperience($event, 'title', index)">
                  {{ expire.title }}
                </div>
                <EditButtons
                  :show-add-btn="false"
                  @remove-click="removeClick(index, 'experience')"
                />
              </div>
              <div class="d-flex justify-content-between">
                <div>
                  <span
                    :contenteditable="editMode"
                    @input="updateExperience($event, 'company', index)"
                    >{{ expire.company }}</span
                  >
                  <span
                    :contenteditable="editMode"
                    @input="updateExperience($event, 'location', index)"
                    >{{ expire.location }}</span
                  >
                </div>
                <div :contenteditable="editMode" @input="updateExperience($event, 'date', index)">
                  {{ expire.date }}
                </div>
              </div>
              <ul>
                <li
                  v-for="(description, indexDescription) in expire.description"
                  :key="indexDescription"
                  :contenteditable="editMode"
                  @input="updateExperienceDescription($event, index, indexDescription)"
                >
                  {{ description }}
                </li>
              </ul>
              <EditButtons
                :show-remove-btn="expire.description.length > 0"
                @add-click="expire.description.push('')"
                @remove-click="expire.description.pop()"
              />
            </div>
          </ResumeSection>
          <ResumeSection>
            <div class="d-flex">
              <SectionHeadline
                :headline="headlines[5]"
                @headline-edited="updateHeadline($event, 5)"
                :editMode="editMode"
              />
              <EditButtons
                @add-click="addEducation"
                :show-remove-btn="false"
                text-add="Add Education"
              />
            </div>
            <div v-for="(item, index) in education" :key="index">
              <div class="d-flex justify-content-between">
                <div :contenteditable="editMode" @input="updateEducation($event, 'title', index)">
                  {{ item.title }}
                </div>
                <EditButtons
                  :show-add-btn="false"
                  @remove-click="removeClick(index, 'education')"
                />
              </div>
              <div class="d-flex justify-content-between">
                <div>
                  <span
                    :contenteditable="editMode"
                    @input="updateEducation($event, 'university', index)"
                  >
                    {{ item.university }}
                  </span>
                  <span
                    :contenteditable="editMode"
                    @input="updateEducation($event, 'location', index)"
                  >
                    {{ item.location }}
                  </span>
                </div>
                <div :contenteditable="editMode" @input="updateEducation($event, 'date', index)">
                  {{ item.date }}
                </div>
              </div>
              <ul>
                <li
                  v-for="(description, index) in item.description"
                  :key="index"
                  :contenteditable="editMode"
                  @input="updateEducationDescription($event, index, indexDescription)"
                >
                  {{ description }}
                </li>
              </ul>
              <EditButtons
                :show-remove-btn="item.description.length > 0"
                @add-click="item.description.push('')"
                @remove-click="item.description.pop()"
              />
            </div>
          </ResumeSection>
        </div>
      </div>
    </div>
  </main>
</template>
<script>
import ResumeSection from '@/components/ResumeSection.vue'
import SectionHeadline from '@/components/SectionHeadline.vue'
import ContactComponent from '@/components/ContactComponent.vue'
import EditButtons from '@/components/EditButtons.vue'
import EditToggle from '@/components/ToggleSwitch.vue'
import SideBar from '@/components/SideBar.vue'
import ColorInput from '@/components/ColorInput.vue'
import PercentInput from '@/components/PercentInput.vue'
import SelectInput from '@/components/SelectInput.vue'
import ImageUpload from '@/components/ImageUpload.vue'
import ExportPdf from '@/components/ExportPdf.vue'
import CustomButtons from './components/CustomButtons.vue'
export default {
  components: {
    ResumeSection,
    SectionHeadline,
    ContactComponent,
    EditButtons,
    EditToggle,
    SideBar,
    ColorInput,
    PercentInput,
    SelectInput,
    ImageUpload,
    ExportPdf,
    CustomButtons
  },
  data() {
    return {
      colors: {
        left: {
          highlight: '#82c0cc',
          text: '#ffffff',
          background: '#3943b7'
        },
        right: {
          highlight: '#3943b7',
          text: '#000505',
          background: '#ffffff'
        }
      },
      name: 'Igor Shpura',
      title: 'Frontend Developer',
      introText:
        'I am a frontend developer with a passion for creating beautiful and user-friendly web applications.',
      imgSrc: '../photo.jpeg',
      headlines: ['About me', 'Contact', 'Skills', 'Certifications', 'Experience', 'Education'],
      contact: {
        phone: '15713909584',
        email: 'spuraigor@gmail.com',
        address: 'Main St 100, 19777 NY'
      },
      skills: ['HTML', 'CSS', 'JavaScript', 'Vue.js', 'React', 'Node.js', 'Express.js'],
      experience: [
        {
          title: 'Frontend Developer',
          company: 'ABC Inc.',
          location: 'New York, NY',
          date: '2020 - Present',
          description: [
            'Developed and maintained responsive web applications using Vue.js and React.',
            'Collaborated with the design team to create user-friendly interfaces.',
            'Implemented RESTful APIs to connect the frontend with the backend.'
          ]
        },
        {
          title: 'Web Designer',
          company: 'XYZ Corp.',
          location: 'Los Angeles, CA',
          date: '2018 - 2020',
          description: [
            'Designed and implemented custom websites for clients using HTML, CSS, and JavaScript.',
            'Optimized websites for performance and SEO.',
            'Collaborated with the design team to create user-friendly interfaces.'
          ]
        }
      ],
      education: [
        {
          title: 'Master of Science in Data Science',
          university: 'StellarTech University',
          location: 'Starville, USA',
          date: '2020 - 2022',
          description: [
            'Coursework included advanced machine learning, statistical modeling, and data visualization techniques.',
            'Thesis: "Predictive Modeling for Customer Churn in E-commerce using Random Forest."'
          ]
        },
        {
          title: 'Bachelor of Science in Computer Science',
          university: 'Evergreen State University',
          location: 'Springdale, USA',
          date: '2012 - 2015',
          description: [
            'Relevant coursework in database management, algorithms, and programming languages.',
            'Senior project: "Development of a Recommender System for Movie Ratings."'
          ]
        }
      ],
      certifications: [
        'Natural Language Processing with Python (Coursera)',
        'Recommendation Systems with TensorFlow on GCP (Google)'
      ],
      editMode: true,
      widthLeft: 30,
      imageShape: 'round',
      headlineWeight: '400',
      showImage: true,
      resumeFormat: 'a4'
    }
  },
  methods: {
    updateHeadline(newValue, index) {
      this.headlines[index] = newValue
    },
    updateProperty(event, key) {
      this[key] = event.target.innerText
    },
    updateNestedProperty(event, key1, key2) {
      this[key1][key2] = event.target.innerText
    },
    updateExperience(event, key, index) {
      this.experience[index][key] = event.target.innerText
    },
    updateExperienceDescription(event, index, descriptionIndex) {
      this.experience[index]['description'][descriptionIndex] = event.target.innerText
    },
    updateEducation(event, key, index) {
      this.education[index][key] = event.target.innerText
    },
    updateEducationDescription(event, index, descriptionIndex) {
      this.education[index]['description'][descriptionIndex] = event.target.innerText
    },
    addClick(value) {
      this[value].push('')
    },
    removeClick(index, value) {
      this[value].splice(index, 1)
    },
    addExperience() {
      this.experience.unshift({
        title: 'Job Title',
        company: 'Company Name',
        location: 'Location',
        date: 'Date',
        description: ['Description']
      })
    },
    addEducation() {
      this.education.unshift({
        title: 'Degree',
        university: 'University Name',
        location: 'Location',
        date: 'Date',
        description: ['Description']
      })
    },
    toggleEditMode(isChecked) {
      this.editMode = isChecked
    },
    toggleImage(isChecked) {
      this.showImage = isChecked
    },
    saveConfig() {
      localStorage.setItem('resumeConfig', JSON.stringify(this.$data))
    }
  },
  created() {
    try {
      const resumeConfig = JSON.parse(localStorage.getItem('resumeConfig'))
      if (resumeConfig) {
        Object.assign(this, resumeConfig)
      }
    } catch (e) {
      console.error('Error parsing saved: ' + e)
    }
  },
  computed: {
    cssVariables() {
      return {
        '--highlight-color-left': this.colors.left.highlight,
        '--text-color-left': this.colors.left.text,
        '--background-color-left': this.colors.left.background,
        '--highlight-color-right': this.colors.right.highlight,
        '--text-color-right': this.colors.right.text,
        '--background-color-right': this.colors.right.background,
        '--headline-weight': this.headlineWeight
      }
    },
    percentageWidthLeft() {
      return `${this.widthLeft}%`
    }
  }
}
</script>
<style scoped>
.resume-wrapper {
  margin-left: auto;
  width: 210mm;
}

#resume {
  box-shadow:
    rgba(50, 50, 93, 0.25) 0px 13px 27px -5px,
    rgba(0, 0, 0, 0.3) 0px 8px 16px -8px;
}

#resume.edit-off {
  /* DIN A4 standard paper size commonly used for resumes
  For North America letter size use width: 8.5in; height: 11in; */

  /* use 296.8mm height instead of 297mm for a4 to prevent of page break */
  height: 297mm;
}

/* if letter format selected */
#resume.edit-off.letter-format {
  width: 8.5in;
  height: 11in;
}

@media (min-width: 1350px) {
  .resume-wrapper {
    margin-left: 300px;
  }
}

@media (min-width: 1600px) {
  .resume-wrapper {
    margin-left: auto;
    margin-right: auto;
  }
}

.left-col {
  background-color: var(--background-color-left);
  color: var(--text-color-left);
  border-right: 1px solid var(--highlight-color-left);
  padding: 30px;
}

.right-col {
  background-color: var(--background-color-right);
  color: var(--text-color-right);
  width: 70%;
  padding: 30px;
}

.personal-name {
  font-weight: 300;
  color: var(--highlight-color-right);
  font-size: 28px;
  border-bottom: 1px solid var(--highlight-color-right);
  margin: 0;
  margin-left: -30px;
  padding-left: 30px;
  padding-bottom: 15px;
}

.personal-title {
  border-bottom: 1px solid var(--highlight-color-right);
  margin: 0 0 20px -30px;
  padding: 15px 0 15px 30px;
  font-weight: 300;
  font-size: 20px;
}

#resume ul {
  padding-inline-start: 16px;
  margin-block-end: 5px;
  margin-block-start: 5px;
}

.profile-pic {
  display: block;
  width: 160px;
  height: 160px;
  border: 5px solid var(--highlight-color-left);
  margin-bottom: 20px;
  object-fit: cover;
  margin-left: auto;
  margin-right: auto;
}

.circle {
  border-radius: 50%;
}

.inner-section {
  margin-bottom: 20px;
}
</style>
