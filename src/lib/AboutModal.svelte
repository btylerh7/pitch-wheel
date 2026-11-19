<script lang="ts">
    import problem from '../assets/the-problem.svg';
    import arcLength from '../assets/arc-length.svg';
</script>

<dialog id="about-modal">
    <button commandFor="about-modal" command="close">Close</button>
    <h1>How this was made</h1>
    <p>
        This may seem like a simple design for a webpage, but it is actually more complex than meets
        the eye. Let's go through it.
    </p>
    <h2>The SVG Setup</h2>
    <p>
        To make the circle-shaped input, I could not use a normal HTML slider input. To make this
        work, I had to do some SVG art. I started with two circles. In SVG terms, the "Stroke" is
        the outer line around the shape. The "fill" is the background color of the shape.
    </p>
    <p>
        In the first circle, the stroke was 20px wide, and its color was white and the fill was
        transparent. This is what that looks like:
    </p>
    <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 100 100" width="100px">
        <circle cx="50" cy="50" r="40" id="circle" stroke="white" stroke-width="20px" fill="none" />
    </svg>
    <p>
        Then, I added a second circle. Now, this circle has the same radius and center coordinates
        as the first one, but it has a stroke of 10px and a stroke color of coral.
    </p>
    <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 100 100" width="100px">
        <circle cx="50" cy="50" r="40" id="circle" stroke="white" stroke-width="20px" fill="none" />
        <circle cx="50" cy="50" r="40" id="circle" stroke="coral" stroke-width="10px" fill="none" />
    </svg>
    <h2>Stroke-Dasharray and Stroke-Dashoffset</h2>
    <p>
        The last important layer is the two attributes of the second circle. <code
            >stroke-dasharray</code
        >
        is used whenever you create a dash pattern in SVGs. If I were to make a dashed line, the dash
        would be this length of the dashes.
    </p>
    <p>Here is a line with length 100 and a <code>stroke-dasharray</code> of 20:</p>

    <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 100 100" width="100px">
        <rect x="0" y="0" stroke="white" width="100" height="100" />
        <path d="M 0 50 l 100 0" stroke-dasharray="20" stroke="coral" />
    </svg>
    <p>Here is a line with length 100 and a <code>stroke-dasharray</code> of 80</p>
    <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 100 100" width="100px">
        <rect x="0" y="0" stroke="white" width="100" height="100" />
        <path d="M 0 50 l 100 0" stroke-dasharray="80" stroke="coral" />
    </svg>
    <p>
        If you set the length of the dasharray property to be the length of the line, it will cover
        the entire line. Then, you can use
        <code>stroke-offset</code> to move the dash a little bit and part of the line is uncovered.
    </p>
    <p>
        Here is the same line with length 100, <code>stroke-dasharray</code> of 80, but with a
        <code>stroke-dashoffset</code> of 30:
    </p>
    <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 100 100" width="100px">
        <rect x="0" y="0" stroke="white" width="100" height="100" />
        <path d="M 0 50 l 100 0" stroke-dasharray="80" stroke="coral" stroke-dashoffset="30" />
    </svg>
    <h2>Enter Circle Math</h2>
    <p>
        To do this with a circle, I needed to get the circumference of the circle. Enter math! The
        circumference of a circle is represented by the following formula:
    </p>
    <p>
        <code>2&#960;r</code>
    </p>
    <p>
        Where <code>r</code> is the radius of the circle. So I calculated this, and set the stroke dasharray
        to the inner circle to this value, then set the offset to the same value:
    </p>
    <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 100 100" width="100px">
        <circle cx="50" cy="50" r="40" id="circle" stroke="white" stroke-width="20px" fill="none" />
        <circle
            cx="50"
            cy="50"
            r="40"
            id="circle"
            stroke-dasharray={2 * Math.PI * 40}
            stroke-dashoffset={2 * Math.PI * 40}
            stroke="coral"
            stroke-width="10px"
            fill="none"
        />
    </svg>
    <p>If you subtract from the offset, it will reveal part of the inner circle:</p>
    <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 100 100" width="100px">
        <circle cx="50" cy="50" r="40" id="circle" stroke="white" stroke-width="20px" fill="none" />
        <circle
            cx="50"
            cy="50"
            r="40"
            id="circle"
            stroke-dasharray={2 * Math.PI * 40}
            stroke-dashoffset={2 * Math.PI * 40 - 40}
            stroke="coral"
            stroke-width="10px"
            fill="none"
        />
    </svg>
    <p>Lastly, I rotated the circle a little bit so the line starts from the top.</p>

    <h2>Filling the Circle</h2>
    <p>
        The idea of the slider is simple enough. You click or drag on the circle and it will fill
        the slider to where your mouse is. The slider will change the pitch that is played.
    </p>
    <p>
        Wait, how do I know how far to move the offset to get to the mouse? How do I know what pitch
        to play?
    </p>
    <h3>Calculating the Angle</h3>
    <p>
        When a user clicks on the circle, we need to get the length of the arc between that point on
        the circle and the center of the circle.
    </p>
    <img
        src={problem}
        alt="A circle with the top right fourth colored coral. The fill starting point is at the top. The user clicked at the middle right of the circle, and the goal is to figure out the length between the two points."
    />
    <img
        src={arcLength}
        alt="The angle of a full circle in radians is 2 times Pi. The length of an arc given an angle (theta) is 's = theta times r.' where r is the radius, which is where we get the formula for the circumference of a circle"
    />
    <p>
        There is a formula for getting the anlge too! It is called the inverse tangent or <code
            >arctangent</code
        >.
    </p>
    <p>
        There is an arctan function in JavaScript called <code>atan</code>, but it has some
        limitations and specifically errors when one of the values is zero. <code>atan2</code>
        was designed to account for this, and it uses the x and y values of the point on the edge of
        the circle
    </p>
    <p>Notably, two weird programming gotchas appear:</p>
    <ol>
        <li>
            The Y axis in JavaScript starts from the top and goes down, which is opposite to a
            normal coordinate plane. So the value for Y needs to be negative
        </li>
        <li>
            Our circle can be anywhere on the screen, so we need to convert the values so the
            circle's center is 0,0. To do this, we just subtract the x coordinate from the center x
            coordinate and the same for the y.
        </li>
    </ol>
    <h2>Putting it all together</h2>
    <p>
        Once you have the length of the circle at the clicked point, you can divide that number by
        the circumference of the circle. That result is the "percentage" of the circle that is
        filled.
    </p>
    <p>
        The percentage can be then used to calculate which pitch to play! If you take the minimum
        pitch (20Hz) and the maximum pitch (8000Hz), pretend like it's a line. Get the length by
        subtracting 20 from 8000. Multiply the percent by the length. That is how far along the line
        you are. Then add that to the minimum pitch.
    </p>
    <h2>In Summary</h2>
    <p>
        I hope that this explanation was of interest to you! You'll always hear people in math class
        saying "When am I ever going to use this?" Well, I certainly found a way to use it!
    </p>
</dialog>

<style>
    code {
        background: hsl(from var(--background) h s 50%);
        border-radius: 5px;
        padding: 0.25rem;
    }
    dialog[open] {
        max-height: 75vh;
        overflow: scroll;
        background: var(--background);
        &::backdrop {
            background: hsl(0 0 0 / 0.5);
            backdrop-filter: blur(5px);
        }
        max-width: 60%;
        margin: auto;
        display: grid;
    }
    button {
        width: fit-content;
    }
    svg {
        margin: auto;
    }
    img {
        width: 100%;
        max-width: 500px;
    }
    ul,
    ol,
    li {
        color: var(--text-color);
    }
</style>
