---
title: 'IOS,JAVA后端配置RSA加密传输'
date: 2018-04-29 18:38:30
tags: 
	- RSA加密
	- Java
---
1.采用openssl生成rsa公钥秘钥，内容如下:
```java
openssl genrsa -out private_key.pem 1024
openssl rsa -in private_key.pem -out rsa_public_key.pem -pubout　　　　　　　　　　　　　
// Create rsa_public_key.pem For Java
openssl pkcs8 -topk8 -in private_key.pem -out pkcs8_private_key.pem -nocrypt　　　　　
// Create pkcs8_private_key.pem For Java
```
2.Java后端代码
```java
package com.zsjyt.common.util;

import org.springframework.core.io.ClassPathResource;
import org.springframework.core.io.Resource;

import javax.crypto.BadPaddingException;
import javax.crypto.Cipher;
import javax.crypto.IllegalBlockSizeException;
import javax.crypto.NoSuchPaddingException;
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.security.*;
import java.security.interfaces.RSAPrivateKey;
import java.security.interfaces.RSAPublicKey;
import java.security.spec.InvalidKeySpecException;
import java.security.spec.PKCS8EncodedKeySpec;
import java.security.spec.X509EncodedKeySpec;

public class EncrypRSAUtils {

    /** 指定公钥存放文件 */
    private static String PUBLIC_KEY_FILE = "rsa_public_key.pem";
    /** 指定私钥存放文件 */
    private static String PRIVATE_KEY_FILE = "pkcs8_private_key.pem";
    /**
     * 私钥
     */
    private static RSAPrivateKey privateKey;

    /**
     * 公钥
     */
    private static RSAPublicKey publicKey;
    /**
     * 获取私钥
     * @return 当前的私钥对象
     */
    public RSAPrivateKey getPrivateKey() {
        return privateKey;
    }
    /**
     * 从字符串中加载公钥
     * @param publicKeyStr 公钥数据字符串
     * @throws Exception 加载公钥时产生的异常
     */
    public static void loadPublicKey(String publicKeyStr) throws Exception{
        try {
            sun.misc.BASE64Decoder decoder = new sun.misc.BASE64Decoder();
            byte[] buffer= decoder.decodeBuffer(publicKeyStr);
            KeyFactory keyFactory= KeyFactory.getInstance("RSA");
            X509EncodedKeySpec keySpec= new X509EncodedKeySpec(buffer);
            publicKey= (RSAPublicKey) keyFactory.generatePublic(keySpec);
        } catch (NoSuchAlgorithmException e) {
            throw new Exception("无此算法");
        } catch (InvalidKeySpecException e) {
            throw new Exception("公钥非法");
        } catch (NullPointerException e) {
            throw new Exception("公钥数据为空");
        }
    }
    /**
     * 从文件中输入流中加载公钥
     * @param in 公钥输入流
     * @throws Exception 加载公钥时产生的异常
     */
    public static void loadPublicKey(InputStream in) throws Exception{
        try {
            BufferedReader br= new BufferedReader(new InputStreamReader(in));
            String readLine= null;
            StringBuilder sb= new StringBuilder();
            while((readLine= br.readLine())!=null){
                if(readLine.charAt(0)=='-'){
                    continue;
                }else{
                    sb.append(readLine);
                    sb.append('\r');
                }
            }
            loadPublicKey(sb.toString());
        } catch (IOException e) {
            throw new Exception("公钥数据流读取错误");
        } catch (NullPointerException e) {
            throw new Exception("公钥输入流为空");
        }
    }
    /**
     * 从文件中加载私钥
     * @param in 私钥文件名
     * @return 是否成功
     * @throws Exception
     */
    public static void loadPrivateKey(InputStream in) throws Exception{
        try {
            BufferedReader br= new BufferedReader(new InputStreamReader(in));
            String readLine= null;
            StringBuilder sb= new StringBuilder();
            while((readLine= br.readLine())!=null){
                if(readLine.charAt(0)=='-'){
                    continue;
                }else{
                    sb.append(readLine);
                    sb.append('\r');
                }
            }
            loadPrivateKey(sb.toString());
        } catch (IOException e) {
            throw new Exception("私钥数据读取错误");
        } catch (NullPointerException e) {
            throw new Exception("私钥输入流为空");
        }
    }

    public static void loadPrivateKey(String privateKeyStr) throws Exception{
        try {
            sun.misc.BASE64Decoder decoder = new sun.misc.BASE64Decoder();
            byte[] buffer= decoder.decodeBuffer(privateKeyStr);
            PKCS8EncodedKeySpec keySpec= new PKCS8EncodedKeySpec(buffer);
            KeyFactory keyFactory= KeyFactory.getInstance("RSA");
            privateKey= (RSAPrivateKey) keyFactory.generatePrivate(keySpec);
        } catch (NoSuchAlgorithmException e) {
            throw new Exception("无此算法");
        } catch (InvalidKeySpecException e) {
            throw new Exception("私钥非法");
        }  catch (NullPointerException e) {
            throw new Exception("私钥数据为空");
        }
    }
    /**
     * 加密
     * @param srcBytes
     * @return
     * @throws NoSuchAlgorithmException
     * @throws NoSuchPaddingException
     * @throws InvalidKeyException
     * @throws IllegalBlockSizeException
     * @throws BadPaddingException
     */
    public static byte[] encrypt(byte[] srcBytes) {
        try {
            Resource resource = new ClassPathResource(PUBLIC_KEY_FILE);
            loadPublicKey(resource.getInputStream());
            if(publicKey== null){
                throw new Exception("加密公钥为空, 请设置");
            }
            Cipher cipher= null;
            cipher= Cipher.getInstance("RSA");
            cipher.init(Cipher.ENCRYPT_MODE, publicKey);
            byte[] output= cipher.doFinal(srcBytes);
            return output;
        } catch (Exception e) {
            e.printStackTrace();
        }
        return  null;
    }

    /**
     * 解密
     * @param srcBytes
     * @return
     * @throws NoSuchAlgorithmException
     * @throws NoSuchPaddingException
     * @throws InvalidKeyException
     * @throws IllegalBlockSizeException
     * @throws BadPaddingException
     */
    public static byte[] decrypt( byte[] srcBytes)  {
        try {
            Resource resource = new ClassPathResource(PRIVATE_KEY_FILE);
            loadPrivateKey(resource.getInputStream());
            if (privateKey== null){
                throw new Exception("解密私钥为空, 请设置");
            }
            Cipher cipher= null;
            cipher= Cipher.getInstance("RSA");
            cipher.init(Cipher.DECRYPT_MODE, privateKey);
            byte[] output= cipher.doFinal(srcBytes);
            return output;
        } catch (Exception e) {
            e.printStackTrace();
        }
        return null;
    }

    public static void main(String[] args) throws IOException {
        //byte[] asas = encrypt("asdasdasd".getBytes());
        sun.misc.BASE64Decoder decoder = new sun.misc.BASE64Decoder();
        byte[] sss = decoder.decodeBuffer("ykB6qWeWdVuDTzmLaeQBuli20Lhe/M8rCC1PsLTdxXt2rUBnt7qZAmnOMMoIzKFpEsxsZ9j9+nwLyZ7+TwEjuC9tyJ7IJm9Pk/LrEk93+sGAquXHk/+qmaNMXZXNxj6Ebog+wNNXMAyTUYhq7KBQsS9myi3ehbkOc7BqkyN7soo=");
        System.out.println(new String(decrypt(sss)));
    }
}
```