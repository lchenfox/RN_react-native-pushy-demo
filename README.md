# RN_react-native-pushy-demo

A demo to keep track of js codes for react-native-pushy.

### Instructions

Because it is rejected by Apple on iOS now, I give up utilizing this repository -- `react-native-pushy`. I already uploaded several versions before and succeeded to publish it via `react-native-pushy`. The following is my codes with `react-native-pushy`. If you want to have a try, please go to the link: [react-native-pushy](https://github.com/reactnativecn/react-native-pushy). Anyway, good luck!

How to write codes for `react-native-pushy`, as follows:

```
import React, {Component} from 'react';
import {
    isFirstTime,
    isRolledBack,
    packageVersion,
    currentVersion,
    checkUpdate,
    downloadUpdate,
    switchVersion,
    switchVersionLater,
    markSuccess,
} from 'react-native-update';

import _updateConfig from '../update.json';
const {appKey} = _updateConfig[Platform.OS];


export default class Root extends Component {

    _checkHotUpdateVersion = (tryAgain = true) => {
        checkUpdate(appKey).then(info => {
            if (info.expired) {
                // Your application version has been udpated, please go to application market for updating: info.downloadUrl && Linking.openURL(info.downloadUrl)
            } else if (info.upToDate) {
                // Your application is up-to-date
                markSuccess();
            } else {
                this._doUpdate(info);
            }
        }).catch(() => {
            if (tryAgain) {
                this._checkHotUpdateVersion(false);
            }
        });
    };

    _doUpdate = (info, tryAgain = true) => {
        downloadUpdate(info).then(hash => {
            if (info.metaInfo) {
                try {
                    const metaInfoObj = JSON.parse(info.metaInfo);
                    const {restartNow} = metaInfoObj;
                    
                    /**
                      If you want to restart app immediately, input "{"restartNow": 1}" while publishing a hot update
                    */
                    if (restartNow) {
                        switchVersion(hash);
                    } else {
                        switchVersionLater(hash);
                    }
                } catch (e) {
                    switchVersionLater(hash);
                }
            } else {
                switchVersionLater(hash);
            }
        }).catch(err => {
            if (tryAgain) {
                this._doUpdate(info, false);
            }
        });
    };
    
}
```