<template>
  <div />
</template>

<script>
import axios from 'axios';

export default {
  name: 'Callback',
  async created() {
    // receives callback to request token
    // code and subdomain are querystring parameters
    // another field is `state` that if sent in the authorize request
    // will be returned back useful if you need to send state data between
    // authorize and callback and also useful to prevent possible csrf attacks
    const { code, subdomain } = this.$route.query;
    const tokenUrl = `https://${subdomain}.thinkific.com/oauth2/token`;
    const tokenResponse = await axios.post(tokenUrl, {
      grant_type: 'authorization_code',
      code_verifier: localStorage.getItem('codeVerifier'),
      code,
    }, {
      auth: {
        username: process.env.VUE_APP_CLIENT_ID,
      },
    });

    // Token response attributes:
    // {
    //   access_token: <access_token>,
    //   expires_in: <seconds to expire>,
    //   gid: <global identifier for site>,
    //   token_type: "bearer"
    // }

    // store token
    localStorage.setItem('accessToken', tokenResponse.data.access_token);

    // store token expiration time
    const expiresAt = this.calculateExpirationTime(tokenResponse.data.expires_in);
    localStorage.setItem('expiresAt', expiresAt);

    // redirect to home
    this.$router.push('/');
  },

  methods: {
    // Stores token expiration time
    calculateExpirationTime(expiresIn) {
      const now = new Date().getTime();
      // sum secods to token generated time
      return now + (expiresIn * 1000);
    },
  },
};
</script>
