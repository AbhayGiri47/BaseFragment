# BaseFragment
create basefragment for your project




abstract class BaseFragment<T : ViewDataBinding> : Fragment() {

    lateinit var binding: T
    abstract fun getContentView(): Int

    abstract fun initView(binding: T)

    abstract fun enableHome(): ImageView?


    override fun onAttach(context: Context?) {
        AndroidSupportInjection.inject(this)
        super.onAttach(context)
    }

    override fun onCreate(savedInstanceState: Bundle?) {

        super.onCreate(savedInstanceState)
        setHasOptionsMenu(false)
    }

    override fun onCreateView(
        inflater: LayoutInflater,
        container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View {
        binding = DataBindingUtil.inflate(inflater, getContentView(), container, false)

        initView(binding)

       enableHome()?.apply {
            visibility = View.VISIBLE
            setOnClickListener {
                startActivity(
                    Intent(
                        activity,
                        DashboardActivity::class.java
                    ).apply {
                        flags = Intent.FLAG_ACTIVITY_NEW_TASK or Intent.FLAG_ACTIVITY_CLEAR_TASK
                    })
            }
        }

        return binding.root
    }

    override fun onStart() {
        super.onStart()
        CommonUtils.hideSoftKeyBoard(activity!!)
    }

    fun showToast(message: String) {
        val toast = Toast.makeText(activity, message, Toast.LENGTH_SHORT)
        toast.setGravity(Gravity.CENTER, 0, 0)
        toast.show()
    }

    fun hideSoftKeyboard(view : View?){

        view?.let {
            val imm = it.context.getSystemService(Context.INPUT_METHOD_SERVICE) as InputMethodManager
            imm.hideSoftInputFromWindow(view.windowToken, 0)
        }


    }


    protected open fun fragmentTransaction(
        transactionType: Int,
        fragment: Fragment,
        container: Int,
        isAddToBackStack: Boolean,
        bundle: Bundle?
    ) {
        if (bundle != null) {
            fragment.arguments = bundle
        }

        val trans = activity!!.supportFragmentManager.beginTransaction()
        when (transactionType) {
            ADD_FRAGMENT -> {
                trans.add(container, fragment, fragment.javaClass.simpleName)
                if (isAddToBackStack) trans.addToBackStack(null)
            }
            REPLACE_FRAGMENT -> {
                trans.replace(container, fragment, fragment.javaClass.simpleName)
                if (isAddToBackStack) trans.addToBackStack(null)
            }
        }
        trans.commit()
    }

    companion object {
        const val ADD_FRAGMENT = 0
        const val REPLACE_FRAGMENT = 1
        const val App_Version = "Version " + BuildConfig.VERSION_NAME

    }
}
