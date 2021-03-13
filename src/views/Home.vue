<template>
  <div class="home">
    <img alt="Thinkific logo" class="logo" src="https://cdn.thinkific.com/assets/thinkific-icon.png">
    <div class="site">
      <label for="site">Thinkific Site:</label>
      <input id="site" class="input" v-model="site" placeholder="https://your-domain.thinkific.com" />
    </div>
    <a class="link" :href="authorizeUrl" :disabled="site != ''">Authorize</a>

    <Token :access-token="accessToken" :expires-at="expiresAt"/>
  </div>
</template>

<script>
import crypto from 'crypto';
import Token from '@/components/Token.vue';

export default {
  name: 'Home',
  components: { Token },
  created() {
    // generate a codeVerifier and store in localStorage
    // for the token retrieve step
    localStorage.setItem('codeVerifier', this.generateCodeVerifier());
  },
  computed: {
    authorizeUrl() {
      // build authorizeUrl to redirect user for consent
      const callbackUrl = `${window.location.href}callback`;
      return `${this.site}/oauth2/authorize?client_id=${
        process.env.VUE_APP_CLIENT_ID
      }&response_type=code&redirect_uri=${
        callbackUrl
      }&code_challenge=${
        this.codeChallenge(this.codeVerifier)
      }&code_challenge_method=S256`;
    },
    codeVerifier() {
      return localStorage.getItem('codeVerifier');
    },
    accessToken() {
      return localStorage.getItem('accessToken');
    },
    expiresAt() {
      return localStorage.getItem('expiresAt');
    },
  },
  data() {
    return {
      // Thinkific Site
      // Custom domain is also allowed
      site: '',
    };
  },
  methods: {
    // transform string to base64 url safe
    base64EncodeUrlSafe(string) {
      return string.toString('base64')
        .replace(/\+/g, '-')
        .replace(/\//g, '_')
        .replace(/=/g, '');
    },
    // generate random codeVerifier
    // recommended a string with between 43 and 128 characters in size
    generateCodeVerifier() {
      return this.base64EncodeUrlSafe(crypto.randomBytes(32));
    },
    // generate codeChallenge
    // Base64UrlSafe(SHA256(codeVerifier))
    codeChallenge(codeVerifier) {
      return this.base64EncodeUrlSafe(this.sha256(codeVerifier));
    },
    // create codeChallenge hash using sha256
    sha256(codeVerifier) {
      return crypto.createHash('sha256').update(codeVerifier).digest();
    },
  },
};
</script>

<style scoped>
.home {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.logo {
  width: 200px;
  height: 200px;
}

label {
  padding-right: 10px;
}

.input {
  width: 300px;
  border-rounded: 1px;
  text-align: center;
  padding: 5px;
  font-size: 1em;
}

.link {
  padding-top: 20px;
  text-decoration: none;
}

.link:hover {
  font-weight: bold;
}

</style>
