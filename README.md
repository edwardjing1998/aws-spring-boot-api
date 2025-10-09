//
// Source code recreated from a .class file by IntelliJ IDEA
// (powered by FernFlower decompiler)
//

package com.fiserv.voltage;

import com.fiserv.dataprotector.DataProtector;
import com.fiserv.dataprotector.exception.CryptoException;
import java.util.ArrayList;
import java.util.List;

class DataProtectorAdapter implements FiservProtector {
    private final String supportedCryptId;
    private final DataProtector dataProtector;
    private final MaskApplier maskApplier;

    public DataProtectorAdapter(String supportedCryptId, DataProtector dataProtector, MaskApplier maskApplier) {
        this.supportedCryptId = supportedCryptId;
        this.dataProtector = dataProtector;
        this.maskApplier = maskApplier;
    }

    public char[] protect(char[] input, String cryptIdName) throws CryptoException {
        return ArrayUtil.isNullOrEmpty(input) ? input : this.dataProtector.protect(input, this.supportedCryptId);
    }

    public char[][] protect(char[][] input, String cryptIdName) throws CryptoException {
        return ArrayUtil.isNullOrEmpty(input) ? input : this.dataProtector.protect(input, this.supportedCryptId);
    }

    public char[] access(char[] input, String cryptIdName) throws CryptoException {
        return ArrayUtil.isNullOrEmpty(input) ? input : this.dataProtector.access(input, this.supportedCryptId);
    }

    public byte[] protect(byte[] input, String cryptIdName) throws CryptoException {
        return ArrayUtil.isNullOrEmpty(input) ? input : this.dataProtector.protect(input, this.supportedCryptId);
    }

    public byte[] access(byte[] input, String cryptIdName) throws CryptoException {
        return ArrayUtil.isNullOrEmpty(input) ? input : this.dataProtector.access(input, this.supportedCryptId);
    }

    public char[][] access(char[][] input, String cryptIdName) throws CryptoException {
        return ArrayUtil.isNullOrEmpty(input) ? input : this.dataProtector.access(input, this.supportedCryptId);
    }

    public char[] accessMasked(char[] input, String cryptIdName) throws CryptoException {
        if (ArrayUtil.isNullOrEmpty(input)) {
            return input;
        } else {
            char[] decrypted = this.access(input, this.supportedCryptId);
            return this.maskApplier.applyMasking(decrypted);
        }
    }

    public char[][] accessMasked(char[][] input, String cryptIdName) throws CryptoException {
        char[][] decrypted = this.access(input, this.supportedCryptId);
        return this.maskApplier.applyMasking(decrypted);
    }

    public String protect(String input, String cryptIdName) throws CryptoException {
        return StringUtil.isNullOrEmpty(input) ? input : new String(this.protect(input.toCharArray(), this.supportedCryptId));
    }

    public List<String> protect(List<String> input, String cryptIdName) throws CryptoException {
        if (StringUtil.isNullOrEmpty(input)) {
            return input;
        } else {
            char[][] plaintext = StringUtil.listToArrays(input);
            char[][] encrypted = this.protect(plaintext, this.supportedCryptId);
            return StringUtil.arraysToList(encrypted);
        }
    }

    public String access(String input, String cryptIdName) throws CryptoException {
        return StringUtil.isNullOrEmpty(input) ? input : new String(this.access(input.toCharArray(), this.supportedCryptId));
    }

    public List<String> access(List<String> inputs, String cryptIdName) throws CryptoException {
        if (StringUtil.isNullOrEmpty(inputs)) {
            return inputs;
        } else {
            List<String> output = new ArrayList(inputs.size());

            for(String input : inputs) {
                output.add(this.access(input, this.supportedCryptId));
            }

            return output;
        }
    }

    public String accessMasked(String input, String cryptIdName) throws CryptoException {
        if (StringUtil.isNullOrEmpty(input)) {
            return input;
        } else {
            char[] decrypted = this.access(input.toCharArray(), this.supportedCryptId);
            return new String(this.maskApplier.applyMasking(decrypted));
        }
    }

    public List<String> accessMasked(List<String> inputs, String cryptIdName) throws CryptoException {
        if (StringUtil.isNullOrEmpty(inputs)) {
            return inputs;
        } else {
            List<String> output = new ArrayList(inputs.size());

            for(String input : inputs) {
                output.add(this.accessMasked(input, this.supportedCryptId));
            }

            return output;
        }
    }

    public void cleanup() {
        this.dataProtector.cleanup();
    }
}
