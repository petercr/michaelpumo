<template>
  <AppLayout
    :title="page.title"
    :description="page.description"
    :colophon="page.colophon"
    :button-id="page.button_id"
    :button-title="page.button_title">
    <template v-for="(slice, index) in page.body">
      <SliceClients
        v-if="slice.type === 'clients'"
        :id="slice.primary.id"
        :key="index"
        :slice="slice"
      />
      <SliceContact
        v-if="slice.type === 'contact'"
        :id="slice.primary.id"
        :key="index"
        :slice="slice"
      />
      <SliceImage
        v-if="slice.type === 'image'"
        :id="slice.primary.id"
        :key="index"
        :slice="slice"
      />
      <SliceProjects
        v-if="slice.type === 'projects'"
        :id="slice.primary.id"
        :key="index"
        :slice="slice"
      />
      <SliceQuotes
        v-if="slice.type === 'quotes'"
        :id="slice.primary.id"
        :key="index"
        :slice="slice"
      />
      <SliceText
        v-if="slice.type === 'text'"
        :id="slice.primary.id"
        :key="index"
        :slice="slice"
      />
    </template>
  </AppLayout>
</template>

<page-query>
  query Page {
    prismic {
      page(uid: "home", lang: "en-gb") {
        meta_title
        meta_description
        meta_keywords
        meta_author
        meta_image
        title
        description
        colophon
        button_id
        button_title
        body {
          ... on prismic_PageBodyClients {
            type
            primary {
              id
            }
            fields {
              name
              role
              logo
            }
          }
          ... on prismic_PageBodyContact {
            type
            primary {
              id
              content
            }
          }
          ... on prismic_PageBodyImage {
            type
            primary {
              id
              image
            }
          }
          ... on prismic_PageBodyProjects {
            type
            primary {
              id
              content
            }
            fields {
              image
              name
              role
              link {
                _linkType
                ... on prismic__ExternalLink {
                  _linkType
                  url
                }
                ... on prismic__Document {
                  _meta {
                    uid
                    type
                  }
                }
                ... on prismic__ImageLink {
                  _linkType
                  url
                }
                ... on prismic__FileLink {
                  _linkType
                  url
                }
              }
            }
          }
          ... on prismic_PageBodyQuotes {
            type
            primary {
              id
            }
            fields {
              quote
              name
              author
            }
          }
          ... on prismic_PageBodyText {
            type
            primary {
              id
              content
            }
          }
        }
      }
    }
  }
</page-query>

<script>
import { slugify } from '@/utils/helpers'
import AppLayout from '@/layouts/AppLayout.vue'
import SliceClients from '@/components/SliceClients/SliceClients.vue'
import SliceContact from '@/components/SliceContact/SliceContact.vue'
import SliceImage from '@/components/SliceImage/SliceImage.vue'
import SliceProjects from '@/components/SliceProjects/SliceProjects.vue'
import SliceQuotes from '@/components/SliceQuotes/SliceQuotes.vue'
import SliceText from '@/components/SliceText/SliceText.vue'

export default {
  name: 'PageIndex',
  className: 'PageIndex',
  components: {
    AppLayout,
    SliceClients,
    SliceContact,
    SliceImage,
    SliceProjects,
    SliceQuotes,
    SliceText
  },
  metaInfo() {
    return ({
      title: this.page.meta_title,
      meta: [
        {
          name: 'description',
          content: this.page.meta_description
        },
        {
          name: 'keywords',
          content: this.page.meta_keywords
        },
        {
          name: 'author',
          content: this.page.meta_author
        },
        {
          property: 'og:title',
          content: this.page.meta_title
        },
        {
          property: 'og:description',
          content: this.page.meta_description
        },
        {
          property: 'og:image',
          content: (this.page.meta_image && Object.prototype.hasOwnProperty.call(this.page.meta_image, 'url')) ? this.page.meta_image.url : null
        },
        {
          property: 'twitter:title',
          content: this.page.meta_title
        },
        {
          property: 'twitter:description',
          content: this.page.meta_description
        },
        {
          property: 'twitter:image:src',
          content: (this.page.meta_image && Object.prototype.hasOwnProperty.call(this.page.meta_image, 'url')) ? this.page.meta_image.url : null
        }
      ]
    })
  },
  computed: {
    page() {
      const payload = { ...this.$page.prismic.page }

      payload.body = payload.body.map(item => {
        if (item.primary && item.primary.id) {
          item.primary.id = slugify(item.primary.id)
        }
        return item
      })

      return payload
    }
  }
}
</script>
