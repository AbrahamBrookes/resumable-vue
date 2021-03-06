# resumable-vue

This is a bare bones implementation of the awesome [Resumable.js](https://github.com/23/resumable.js) library for chunked uploads using vue 2. This project was generated by vue-cli and so comes with the standard commands. I haven't added any tests yet.

This project mainly serves as an example of one way you could set up resumable to run in a vue environment. If you want to use this as a jumping off point for your own project (without pulling and running this repo) here's how you can do that:

1. Copy the two components `UploadVideos.vue` and `UploadingVideo.vue` from `src/components` to your project (maybe also copy `public/cloud-computing.svg` if you want to use that icon - attribution [Smartline from flaticon](https://www.flaticon.com/authors/smartline))
2. Install Resumablejs via `yarn add resumablejs` or `npm install resumablejs`
3. Add the `UploadVideos` component where you want it to go
4. Implement your own backend! 
 
I set this up in a laravel project and installed [pionl/laravel-chunk-upload](https://github.com/pionl/laravel-chunk-upload) and just copy-pasted their [example upload controller](https://github.com/pionl/laravel-chunk-upload-example/blob/master/example/src/Http/Controllers/UploadController.php) without any changes, and set up a route to point at UploadController->upload() and it worked straight out of the box! Note that you'll want to disable rate limiting on your upload route, as resumablejs calls your endpoint a _lot_. You also want to avoid using your web middleware on that route as well if you get 419 error responses.

Enjoy!

## Project setup
```
yarn install
```

### Compiles and hot-reloads for development
```
yarn serve
```

### Compiles and minifies for production
```
yarn build
```

### Run your unit tests
```
yarn test:unit
```

### Customize configuration
See [Configuration Reference](https://cli.vuejs.org/config/).
