<template>
    <div :class="classes">
        <label :class="[prefixCls + '-label']" :for="labelFor" :style="labelStyles" v-if="label || $slots.label"><slot name="label">{{ label }}</slot></label>
        <div :class="[prefixCls + '-content']" :style="contentStyles">
            <slot></slot>
            <transition name="fade">
                <div :class="[prefixCls + '-error-tip']" v-if="validateState === 'error' && showMessage && form.showMessage">{{ validateMessage }}</div>
            </transition>
        </div>
    </div>
</template>
<script>
    import AsyncValidator from 'async-validator';
    import Emitter from '../../mixins/emitter';

    const prefixCls = 'ivu-form-item';

    function getPropByPath(obj, path) {

        let tempObj = obj;
        // 对象属性取值可以是obj.key或者obj[key]，prop也可以是这个格式
        path = path.replace(/\[(\w+)\]/g, '.$1');
        path = path.replace(/^\./, '');

        let keyArr = path.split('.');
        let i = 0;
        // 一层一层的取值和赋值，直到取到目标值
        for (let len = keyArr.length; i < len - 1; ++i) {
            let key = keyArr[i];
            if (key in tempObj) {
                tempObj = tempObj[key];
            } else {
                throw new Error('[iView warn]: please transfer a valid prop path to form item!');
            }
        }
        return {
            o: tempObj,
            k: keyArr[i],
            v: tempObj[keyArr[i]]
        };
    }

    export default {
        name: 'FormItem',
        mixins: [ Emitter ],
        props: {
            label: {
                type: String,
                default: ''
            },
            labelWidth: {
                type: Number
            },
            prop: {
                type: String
            },
            required: {
                type: Boolean,
                default: false
            },
            rules: {
                type: [Object, Array]
            },
            error: {
                type: String
            },
            validateStatus: {
                type: Boolean
            },
            showMessage: {
                type: Boolean,
                default: true
            },
            labelFor: {
                type: String
            }
        },
        data () {
            return {
                prefixCls: prefixCls,
                isRequired: false,
                validateState: '',
                validateMessage: '',
                validateDisabled: false,
                validator: {}
            };
        },
        watch: {
            error: {
                handler (val) {
                    this.validateMessage = val;
                    this.validateState = val ? 'error' : '';
                },
                immediate: true
            },
            validateStatus (val) {
                this.validateState = val;
            },
            rules (){
                this.setRules();
            }
        },
        inject: ['form'],
        computed: {
            // 整个组件四种状态required、error、validating、default
            classes () {
                return [
                    `${prefixCls}`,
                    {
                        [`${prefixCls}-required`]: this.required || this.isRequired,
                        [`${prefixCls}-error`]: this.validateState === 'error',
                        [`${prefixCls}-validating`]: this.validateState === 'validating'
                    }
                ];
            },
            // form() {
            //    let parent = this.$parent;
            //    while (parent.$options.name !== 'iForm') {
            //        parent = parent.$parent;
            //    }
            //    return parent;
            // },
            fieldValue () {
                const model = this.form.model;
                if (!model || !this.prop) { return; }

                let path = this.prop;
                // 可能是传prop时可以写成"xxx:xxx:x""
                if (path.indexOf(':') !== -1) {
                    path = path.replace(/:/, '.');
                }

                return getPropByPath(model, path).v;
            },
            // 表单域标签的宽度，所有的 FormItem 都会继承 Form 组件的 label-width 的值
            labelStyles () {
                let style = {};
                const labelWidth = this.labelWidth === 0 || this.labelWidth ? this.labelWidth : this.form.labelWidth;

                if (labelWidth || labelWidth === 0) {
                    style.width = `${labelWidth}px`;
                }
                return style;
            },
            // 表单域标签的宽度，所有的 FormItem 都会继承 Form 组件的 label-width 的值
            contentStyles () {
                let style = {};
                const labelWidth = this.labelWidth === 0 || this.labelWidth ? this.labelWidth : this.form.labelWidth;

                if (labelWidth || labelWidth === 0) {
                    style.marginLeft = `${labelWidth}px`;
                }
                return style;
            }
        },
        methods: {
            setRules() {
                let rules = this.getRules();
                if (rules.length&&this.required) {
                    return;
                }else if (rules.length) {
                    rules.every((rule) => {
                        this.isRequired = rule.required;
                    });
                }else if (this.required){
                    this.isRequired = this.required;
                }
                this.$off('on-form-blur', this.onFieldBlur);
                this.$off('on-form-change', this.onFieldChange);
                this.$on('on-form-blur', this.onFieldBlur);
                this.$on('on-form-change', this.onFieldChange);
            },
            // 优先使用form-item的rules,其次是form的rules。是怎么取到多层对象的属性的
            getRules () {
                let formRules = this.form.rules;
                const selfRules = this.rules;

                formRules = formRules ? formRules[this.prop] : [];

                return [].concat(selfRules || formRules || []);
            },
            getFilteredRule (trigger) {
                const rules = this.getRules();
                // 过滤了rules中无trigger或者无form组件中传的trigger
                return rules.filter(rule => !rule.trigger || rule.trigger.indexOf(trigger) !== -1);
            },
            // 在form触发validate时会传trigger但是是个''
            validate(trigger, callback = function () {}) {
                // ？
                this.$nextTick(() => {
                    let rules = this.getFilteredRule(trigger);
                    if (!rules || rules.length === 0) {
                        if (!this.required) {
                            callback();
                            return true;
                        }else {
                            rules = [{required: true}];
                        }
                    }

                    this.validateState = 'validating';

                    let descriptor = {};
                    descriptor[this.prop] = rules;
                    // async-validator接收一个rule对象或者方法
                    const validator = new AsyncValidator(descriptor);
                    let model = {};

                    model[this.prop] = this.fieldValue;
                    // 这里已经明确了只要触发第一个规则就会不再继续触发余下规则
                    // 还有个first配置是说如果有多个异步验证，true可以在第一个验证结果回来后就不再用余下验证结果
                    validator.validate(model, { firstFields: true }, errors => {
                        this.validateState = !errors ? 'success' : 'error';
                        this.validateMessage = errors ? errors[0].message : '';

                        callback(this.validateMessage);
                    });
                    this.validateDisabled = false;
                });
            },
            resetField () {
                this.validateState = '';
                this.validateMessage = '';

                let model = this.form.model;
                let value = this.fieldValue;
                let path = this.prop;
                if (path.indexOf(':') !== -1) {
                    path = path.replace(/:/, '.');
                }

                let prop = getPropByPath(model, path);

//                if (Array.isArray(value) && value.length > 0) {
//                    this.validateDisabled = true;
//                    prop.o[prop.k] = [];
//                } else if (value !== this.initialValue) {
//                    this.validateDisabled = true;
//                    prop.o[prop.k] = this.initialValue;
//                }
                if (Array.isArray(value)) {
                    this.validateDisabled = true;
                    prop.o[prop.k] = [].concat(this.initialValue);
                } else {
                    this.validateDisabled = true;
                    prop.o[prop.k] = this.initialValue;
                }
            },
            // 只支持两种事件取验证blur和change
            onFieldBlur() {
                this.validate('blur');
            },
            onFieldChange() {
                if (this.validateDisabled) {
                    this.validateDisabled = false;
                    return;
                }

                this.validate('change');
            }
        },
        mounted () {
            if (this.prop) {
                // 每个form-item都有自己的validate方法，在整个form校验时遍历form，每个都执行一次validate
                this.dispatch('iForm', 'on-form-item-add', this);

                Object.defineProperty(this, 'initialValue', {
                    value: this.fieldValue
                });

                this.setRules();
            }
        },
        beforeDestroy () {
            this.dispatch('iForm', 'on-form-item-remove', this);
        }
    };
</script>
