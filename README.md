//
// Source code recreated from a .class file by IntelliJ IDEA
// (powered by FernFlower decompiler)
//

package com.fiserv.voltage;

import com.fiserv.dataprotector.exception.CryptoException;
import java.util.List;

public interface FiservProtector {
    String protect(String var1, String var2) throws CryptoException;

    char[] protect(char[] var1, String var2) throws CryptoException;

    List<String> protect(List<String> var1, String var2) throws CryptoException;

    char[][] protect(char[][] var1, String var2) throws CryptoException;

    byte[] protect(byte[] var1, String var2) throws CryptoException;

    String access(String var1, String var2) throws CryptoException;

    char[] access(char[] var1, String var2) throws CryptoException;

    List<String> access(List<String> var1, String var2) throws CryptoException;

    char[][] access(char[][] var1, String var2) throws CryptoException;

    String accessMasked(String var1, String var2) throws CryptoException;

    char[] accessMasked(char[] var1, String var2) throws CryptoException;

    List<String> accessMasked(List<String> var1, String var2) throws CryptoException;

    char[][] accessMasked(char[][] var1, String var2) throws CryptoException;

    byte[] access(byte[] var1, String var2) throws CryptoException;

    void cleanup();
}
