---
id: style
title: Хэлбэржүүлэлт
---

React Native-д хэлбэржүүлэлт хийхийн тулд ямар нэг онцгой хэл, синтакс шаардлагагүй. Ердөө Javascript ашиглан аппликейшныхаа хэв загварыг гаргахад болно. Бүх гол компонент нь `style` нэртэй пропыг зөвшөөрдөг. 
Хэлбэржүүлэлтийн нэр, өнгө нь [values](colors.md) веб дээр CSS хэрхэн ажилладагтай адилхан.  Гэхдээ `background-color` гэхийн оронд `backgroundColor` гэх мэт, үгийн дунд томоор бичсэн эсэх нь хамаарахгүй.  

`style` нэртэй проп нь цулгуй, хуучны Javascript байж болно. Энэ нь хамгийн энгийн бөгөөд бид ихэвчлэн жишиг код болгон ашигладаг. Мөн та хэлбэржүүлэлтийн массив ашиглаж болно. Массивийн хамгийн сүүлийн хэлбэржүүлэлт нь эхэнд нь гарч ирдэг. Өмнө нь ашигласан хэлбэржүүлэлтээ дахин ашиглах болон энэ функцийг ашиглах боломжтой. 

Компонент нь илүү төвөгтэй болж ирдэг учир нэг газар хэд хэдэн хэлбэржүүлэлт тодорхойлох бол `StyleSheet.create`-ийг ашиглавал илүү цэвэрхэн байдаг. Жишээ нь:  

```ReactNativeWebPlayer
import React, { Component } from 'react';
import { AppRegistry, StyleSheet, Text, View } from 'react-native';

const styles = StyleSheet.create({
  bigBlue: {
    color: 'blue',
    fontWeight: 'bold',
    fontSize: 30,
  },
  red: {
    color: 'red',
  },
});

export default class LotsOfStyles extends Component {
  render() {
    return (
      <View>
        <Text style={styles.red}>just red</Text>
        <Text style={styles.bigBlue}>just bigBlue</Text>
        <Text style={[styles.bigBlue, styles.red]}>bigBlue, then red</Text>
        <Text style={[styles.red, styles.bigBlue]}>red, then bigBlue</Text>
      </View>
    );
  }
}

// skip this line if using Create React Native App
AppRegistry.registerComponent('AwesomeProject', () => LotsOfStyles);
```
Компонентүүээ `style` пропыг хүлээн зөвшөөрдөг болгочихвол эргээд дэд компонентүүдийнхаа хэлбэржүүлэлтийг гаргахад ашиглаж болно. Мөн үүнийг CSS доторх шиг "cascade" маягтай болгоход ашиглаж болно.

Текстийн хэлбэржүүлэлтийг өөрчлөх олон янзын арга бий. [Text component reference](text.md) гэсэн рүү орж бүрэн жагсаалтыг харна уу.

Одоо та текстийг хэрхэн гоёмсог болгох тухай мэдсэн тул дараа нь 
 [Компонентын хэмжээг хэрхэн удирдах](height-and-width.md) тухай мэдээлэлтэй танилцацгаая.
